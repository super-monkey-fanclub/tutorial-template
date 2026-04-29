
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

.. code-block:: dart

  class _HomePageState extends State<HomePage> {
    static const String _sessionUserStorageKey = 'unify.current_user';

This section of code uses a const key for saving/loading the user login data by storing it in local storage.

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
