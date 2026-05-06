
Frontend
=============

Overview
--------

The front end of this application is different each device. This doc will cover some of the front end choices and inner workings, but in terms of what you can expect to see throughout the app, you can find:

Fonts
-----

Android: Roboto

iOS/macOS: San Francisco

Windows: Segoe UI

Linux/web: platform/browser default sans-serif chosen by Flutter


Colours
-------

One consistent colour theme, featuring the colours represented in the University of Portsmouth logo.

Seed colour: 0xFF003087

Primary colour: 0xFF003087

Secondary colour: 0xFF7B2D8E

Background colour: 0x29FFFFFF 

Error colour: 0xFFBA1A1A

Text colour: Not fixed, readable contrast via code:

.. code-block:: dart

  style: Theme.of(context).textTheme.headlineSmall?.copyWith(
    fontWeight: FontWeight.w700,

main.dart
----------


.. _theme:

Theme
------

.. code-block:: dart

  class UnifyApp extends StatelessWidget {
    const UnifyApp({super.key});
  
    @override
    Widget build(BuildContext context) {
      return MaterialApp(
        title: 'Unify - University of Portsmouth Societies',
        theme: ThemeData(
          colorScheme: ColorScheme.fromSeed(
            seedColor: const Color(0xFF003087), // UoP Blue
            primary: const Color(0xFF003087), // UoP Blue
            secondary: const Color(0xFF7B2D8E), // UoP Purple
          ),
          appBarTheme: const AppBarTheme(
            foregroundColor: Colors.white,
            iconTheme: IconThemeData(color: Colors.white),
          ),
          useMaterial3: true,
        ),
        home: const HomePage(),
      );
    }

This section of code initialises the color scheme throughout the whole program, the navigation bar color scheme, and ensures it is the first page shown to the user when starting the app. The colour scheme is decided through the use of colorScheme.fromSeed(), which chooses 3 colours to base the theme off of. For the Navigation bar, its theme is tetermined by foregroundColor and iconTheme. To ensure the first page is the home page, we use home:const HomePage().

.. _loginData:

User login data
----------------

.. code-block:: dart

  class _HomePageState extends State<HomePage> {
    static const String _sessionUserStorageKey = 'unify.current_user';

This section of code uses a const key for saving/loading the user login data by storing it in local storage.

.. _navBar:

Navigation Bar
---------------

.. code-block:: dart

  IconButton(
            tooltip: 'Account',
            onPressed: _openAuthPage,
            icon: const Icon(Icons.person, color: Colors.white),
          ),
          TextButton(
            onPressed: _openAboutUsPage,
            child: const Text(
              'About Us',
              style: TextStyle(color: Colors.white, fontSize: 16),
            ),
          ),
        ],
        bottom: PreferredSize(
          preferredSize: Size.fromHeight(_showHeaderSearch ? 72 : 0),
          child: _showHeaderSearch
              ? Padding(
                  padding: const EdgeInsets.fromLTRB(16, 0, 16, 12),
                  child: TextField(
                    controller: _searchController,
                    focusNode: _searchFocusNode,
                    textInputAction: TextInputAction.search,
                    onSubmitted: _openSearchResultsPage,
                    decoration: InputDecoration(
                      hintText: 'Search societies, e.g. "Art", "Gaming"',
                      prefixIcon: const Icon(Icons.search),
                      suffixIcon: IconButton(
                        icon: const Icon(Icons.arrow_forward),
                        onPressed: _openSearchResultsPage,
                      ),
                      border: OutlineInputBorder(
                        borderRadius: BorderRadius.circular(14.0),
                        borderSide: BorderSide.none,
                      ),
                      filled: true,
                      fillColor: Colors.white,
                    ),
                  ),
                )
              : const SizedBox.shrink(),
        ),
      ),

This section of code creates the Navigation Bar at the top of the page. It creates a Account button which links to the login page, an About us button which links to the About Us page, and a search bar used to search for societies. These are all in the far right of the navigation bar, and the search bar extends when pressed. To prompt the user to search for societies, we use the hintText paramater to encourage user to search for societies.

.. _welcomeText: 

Intro Text
-----------

.. code-block:: dart

  body: SafeArea(
      child: SingleChildScrollView(
        padding: const EdgeInsets.fromLTRB(16, 14, 16, 24),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text(
              'Welcome${_currentUser == null ? '' : ', ${_currentUser!['name'] ?? 'back'}'}',
              style: Theme.of(context).textTheme.headlineSmall?.copyWith(
                fontWeight: FontWeight.w700,
              ),
            ),
            const SizedBox(height: 8),
            Text(
              'Find societies that match your interests and connect with students faster.',
              style: Theme.of(
                context,
              ).textTheme.bodyMedium?.copyWith(height: 1.35),
            ),

This code displays the text at the top of the page, "Welcome" and "Find societies that match your interests and connect with students faster". The welcome string is optionally personalised to show a plain "Welcome" when _currentUser is null, or show "Welcome, <name>" when _currentUser exists. If the stored name is null it falls back to "back". The secondary text in the SizedBox adds a height of 8 for vertical spacing.

.. _socList: 

Joined societies list - Logged in + Not in any society
-------------------------------------------------------


.. code-block:: dart

  if (joined.isEmpty) {
      return Card(
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(12),
        ),
        child: Padding(
          padding: const EdgeInsets.all(12.0),
          child: Row(
            children: [
              const Icon(Icons.info_outline),
              const SizedBox(width: 12),
              Expanded(
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    const Text(
                      'You haven\'t joined any societies yet',
                      style: TextStyle(
                        fontWeight: FontWeight.w700,
                      ),
                    ),
                    const SizedBox(height: 6),
                    Text(
                      'Tap "Find societies" to browse and join groups.',
                      style: TextStyle(
                        color: Colors.grey.shade700,
                      ),
                    ),
                  ],
                ),
              ),
              TextButton(
                onPressed: _openSocietiesPage,
                child: const Text('Find societies'),
              ),
            ],
          ),
        ),
      );
    }

If you are logged in and not in a society, a menu appears which prompts the users to search for societies and connect with more students. This is checked through the if statement joined.IsEmpty - if the current user has no joined societies, returns a Card shown in-place promopting the user to join a society. Tapping the TextButton calls _openSocietiesPage, which navigates to the societies browsing page.

Joined societies list - Logged in + In a society
------------------------------------------------

.. code-block:: dart

    return Column(
      crossAxisAlignment: CrossAxisAlignment.start,
      children: [
        Text(
          'Your societies',
          style: Theme.of(context).textTheme.titleMedium
              ?.copyWith(fontWeight: FontWeight.w700),
        ),
        const SizedBox(height: 8),
        SizedBox(
          height: 140,
          child: ListView.separated(
            scrollDirection: Axis.horizontal,
            itemCount: joined.length,
            separatorBuilder: (_, __) =>
                const SizedBox(width: 12),
            itemBuilder: (context, index) {
              final s = joined[index];
              return InkWell(
                onTap: () => _navigateToSocietyDetails(
                  s,
                  initialJoined: true,
                ),
                child: SizedBox(
                  width: 220,
                  child: Card(
                    clipBehavior: Clip.antiAlias,
                    shape: RoundedRectangleBorder(
                      borderRadius: BorderRadius.circular(12),
                    ),
                    child: Padding(
                      padding: const EdgeInsets.all(12.0),
                      child: Row(
                        children: [
                          CircleAvatar(
                            radius: 28,
                            child: Icon(s.icon, size: 28),
                          ),
                          const SizedBox(width: 12),
                          Expanded(
                            child: Column(
                              crossAxisAlignment:
                                  CrossAxisAlignment.start,
                              mainAxisAlignment:
                                  MainAxisAlignment.center,
                              children: [
                                Text(
                                  s.name,
                                  style: const TextStyle(
                                    fontWeight: FontWeight.w700,
                                  ),
                                  overflow:
                                      TextOverflow.ellipsis,
                                ),
                                const SizedBox(height: 6),
                                Text(
                                  '${s.memberCount} members · ${s.rating} ★',
                                  maxLines: 1,
                                  overflow: TextOverflow.ellipsis,
                                  style: TextStyle(
                                    color: Colors.grey.shade700,
                                  ),
                                ),
                              ],
                            ),
                          ),
                        ],
                      ),
                    ),
                  ),
                ),
              );
            },
          ),
        ),
        const SizedBox(height: 12),
      ],
    );
  },

If you are logged in, then you can view the list of societies. There is one Card per joined society.

Not Logged in
--------------

.. code-block:: dart

  if (_currentUser == null) ...[
    const SizedBox(height: 8),
    Card(
      shape: RoundedRectangleBorder(
        borderRadius: BorderRadius.circular(12),
      ),
      child: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Row(
          children: [
            const Icon(Icons.login),
            const SizedBox(width: 12),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                mainAxisSize: MainAxisSize.min,
                children: [
                  const Text(
                    'Sign in to see your societies',
                    style: TextStyle(fontWeight: FontWeight.w700),
                  ),
                  const SizedBox(height: 6),
                  Text(
                    'Create an account or sign in to quickly access societies you\'ve joined.',
                    style: TextStyle(color: Colors.grey.shade700),
                  ),
                ],
              ),
            ),
            TextButton(
              onPressed: _openAuthPage,
              child: const Text('Sign in'),
            ),
          ],
        ),
      ),
    ),
    const SizedBox(height: 12),
  ],

If the user is not logged in, the box prompts the user to sign in or create an account to quickly access societies you've joined. We can see this by checking if _currentUser == null, meaning that the user has not logged in. Pressing the text button opens the login page, where you can either log in or sign up.

.. _heroCarousel:

Hero Carousel
---------------

.. code-block:: dart

 const SizedBox(height: 18),
  Text(
    'Featured this week',
    style: Theme.of(
      context,
    ).textTheme.titleLarge?.copyWith(fontWeight: FontWeight.w700),
  ),
  const SizedBox(height: 6),
  Text(
    'Explore popular student groups on campus',
    style: TextStyle(color: Colors.grey.shade700),
  ),

This text is before the hero carousel and tells the user what to expect. It uses text to display "Featured this week".

.. code-block:: dart

  class _HeroCarouselState extends State<HeroCarousel> {
  final PageController _pageController = PageController();
  int _currentPage = 0;
  Timer? _timer;
  bool _hovering = false;
  List<Society> get _societies => widget.societies;

  void _openSocietyDetails(Society society) {
    Navigator.of(context).push(
      MaterialPageRoute(
        builder: (_) => SocietyDetailsPage(
          name: society.name,
          description: society.description,
          imageUrl: society.imageUrl,
          icon: society.icon,
          initialMemberCount: society.memberCount,
          initialAverageRating: society.rating,
          userEmail: widget.userEmail,
          userAuthToken: widget.userAuthToken,
        ),
      ),
    );
  }

This is the instantiation of the Hero Carousel. It takes the society name, description, imageURL, icon, member count, average rating, user email and their auth token. The hero carousel generates 14 societies with all this information and automatically scrolls through each one automatically, allowing users to quickly view societies at a glance. Clicking on the society in the hero carousel brings you to the society page, where the user can join the society.

about_us.dart
--------------

Banner
------

.. code-block:: dart

  class AboutUsPage extends StatelessWidget {
  const AboutUsPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.primary,
        title: const Text(
          'About Us',
          style: TextStyle(color: Colors.white),
        ),
        iconTheme: const IconThemeData(color: Colors.white),
      ),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(24.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            // Hero banner
            Container(
              width: double.infinity,
              padding: const EdgeInsets.all(24.0),
              decoration: BoxDecoration(
                gradient: LinearGradient(
                  begin: Alignment.topLeft,
                  end: Alignment.bottomRight,
                  colors: [
                    Theme.of(context).colorScheme.primary,
                    Theme.of(context).colorScheme.secondary,
                  ],
                ),
                borderRadius: BorderRadius.circular(16),
              ),
              child: const Column(
                children: [
                  Icon(Icons.groups, size: 64, color: Colors.white),
                  SizedBox(height: 12),
                  Text(
                    'Unify',
                    style: TextStyle(
                      color: Colors.white,
                      fontSize: 32,
                      fontWeight: FontWeight.bold,
                    ),
                  ),
                  SizedBox(height: 8),
                  Text(
                    'Connecting students through societies',
                    style: TextStyle(
                      color: Colors.white70,
                      fontSize: 16,
                    ),
                    textAlign: TextAlign.center,
                  ),
                ],
              ),
            ),

This code creates the main banner. It is located in the top centre of the page, with large white text saying "About Us"s

