
Frontend
=============

Overview
=============

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

Pages
======

main.dart
---------

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
  }

This section of code initialises the color scheme throughout the whole program, the navigation bar color scheme, annd ensures it is the first page shown to the user when starting the app.

User login data
----------------

.. code-block:: dart

  class _HomePageState extends State<HomePage> {
    static const String _sessionUserStorageKey = 'unify.current_user';

This section of code uses a const key for saving/loading the user login data by storing it in local storage.

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

This section of code creates the Navigation Bar at the top of the page. It creates a Account button which links to the login page, an About us button which links to the About Us page, and a search bar used to search for societies. These are all in the far right of the navigation bar, and the search bar extends when pressed. 

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

This code displays the text at the top of the page, "Welcome" and "Find societies that match your interests and connect with students faster"

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

If you are logged in and not in a society, a menu appears which prompts the users to search for societies and connect with more students

Joined societies list - Logged in + In a society
------------------------------------------------

..code-block:: dart

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

If you are logged in, then you can view the list of societies.

Not Logged in
--------------

..code-block:: dart

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

If the user is not logged in, the box prompts the user to sign in or create an account to quickly access societies you've joined.
