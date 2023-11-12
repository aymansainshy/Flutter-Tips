
Before we start guys - please we offer mobile app development service @venbrinoDev
you can send us a dm 



1. Make sure to initialize Firebase - 
2. Add dependency flutter pub add firebase_messaging
3. Configure your app for ios/macos to receive notification (https://firebase.flutter.dev/docs/messaging/apple-integration/)

## Usage

```dart

import 'package:flutter/material.dart';
import 'package:go_router/go_router.dart';

main() {
  CustomNavigationHelper.instance;
  runApp(const App());
}

class App extends StatelessWidget {
  const App({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp.router(
      debugShowCheckedModeBanner: false,
      routerConfig: CustomNavigationHelper.router,
    );
  }
}

class CustomNavigationHelper {


  static final CustomNavigationHelper _instance = CustomNavigationHelper._internal();

  static CustomNavigationHelper get instance => _instance;

  static late final GoRouter router;

  static final GlobalKey<NavigatorState> parentNavigatorKey = GlobalKey<NavigatorState>();
  static final GlobalKey<NavigatorState> homeTabNavigatorKey = GlobalKey<NavigatorState>();
  static final GlobalKey<NavigatorState> searchTabNavigatorKey = GlobalKey<NavigatorState>();
  static final GlobalKey<NavigatorState> settingsTabNavigatorKey = GlobalKey<NavigatorState>();

  BuildContext get context => router.routerDelegate.navigatorKey.currentContext!;

  GoRouterDelegate get routerDelegate => router.routerDelegate;

  GoRouteInformationParser get routeInformationParser => router.routeInformationParser;

  static const String signUpPath = '/signUp';
  static const String signInPath = '/signIn';
  static const String detailPath = '/detail';
  static const String rootDetailPath = '/rootDetail';

  static const String homePath = '/home';
  static const String settingsPath = '/settings';
  static const String searchPath = '/search';

  factory CustomNavigationHelper() {
    return _instance;
  }



  CustomNavigationHelper._internal() {
    final routes = [
      StatefulShellRoute.indexedStack(
        parentNavigatorKey: parentNavigatorKey,
        branches: [
          StatefulShellBranch(
            navigatorKey: homeTabNavigatorKey,
            routes: [
              GoRoute(
                path: homePath,
                pageBuilder: (context, GoRouterState state) {
                  return getPage(
                    child: const HomePage(),
                    state: state,
                  );
                },
              ),
            ],
          ),
          StatefulShellBranch(
            navigatorKey: searchTabNavigatorKey,
            routes: [
              GoRoute(
                path: searchPath,
                pageBuilder: (context, state) {
                  return getPage(
                    child: const SearchPage(),
                    state: state,
                  );
                },
              ),
            ],
          ),
          StatefulShellBranch(
            navigatorKey: settingsTabNavigatorKey,
            routes: [
              GoRoute(
                path: settingsPath,
                pageBuilder: (context, state) {
                  return getPage(
                    child: const SettingsPage(),
                    state: state,
                  );
                },
              ),
            ],
          ),
        ],
        pageBuilder: (
          BuildContext context,
          GoRouterState state,
          StatefulNavigationShell navigationShell,
        ) {
          return getPage(
            child: BottomNavigationPage(
              child: navigationShell,
            ),
            state: state,
          );
        },
      ),
      GoRoute(
        parentNavigatorKey: parentNavigatorKey,
        path: signUpPath,
        pageBuilder: (context, state) {
          return getPage(
            child: const SignUpPage(),
            state: state,
          );
        },
      ),
      GoRoute(
        parentNavigatorKey: parentNavigatorKey,
        path: signInPath,
        pageBuilder: (context, state) {
          return getPage(
            child: const SignInPage(),
            state: state,
          );
        },
      ),
      GoRoute(
        path: detailPath,
        pageBuilder: (context, state) {
          return getPage(
            child: const DetailPage(),
            state: state,
          );
        },
      ),
      GoRoute(
        parentNavigatorKey: parentNavigatorKey,
        path: rootDetailPath,
        pageBuilder: (context, state) {
          return getPage(
            child: const DetailPage(),
            state: state,
          );
        },
      ),
    ];

    router = GoRouter(
      navigatorKey: parentNavigatorKey,
      initialLocation: signUpPath,
      routes: routes,
    );
  }

  static Page getPage({
    required Widget child,
    required GoRouterState state,
  }) {
    return MaterialPage(
      key: state.pageKey,
      child: child,
    );
  }
}











class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Home"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.detailPath,
                );
              },
              child: const Text('Push Detail'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Using push method of router enable us to navigate in the current tab without losing the shell',
                textAlign: TextAlign.center,
              ),
            ),

            /// TODO continue
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.rootDetailPath,
                );
              },
              child: const Text('Push Detail From Root Navigator'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Using push method of router enable us to navigate in the current tab without losing the shell',
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class DetailPage extends StatelessWidget {
  const DetailPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Detail"),
      ),
    );
  }
}

class SignUpPage extends StatelessWidget {
  const SignUpPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("SignUp"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.signInPath,
                );
              },
              child: const Text('Push SignIn'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Using push method of router enable us to go back functionality',
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class SignInPage extends StatelessWidget {
  const SignInPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("SignIn"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.homePath,
                );
              },
              child: const Text('Push Home Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Using push method of router enable us to push that page as standalone page instead of showing with Shell',
                textAlign: TextAlign.center,
              ),
            ),
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.go(
                  CustomNavigationHelper.homePath,
                );
              },
              child: const Text('Go Home Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Instead if we use go method of router we will have the home page with the Shell',
                textAlign: TextAlign.center,
              ),
            ),
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.go(
                  CustomNavigationHelper.searchPath,
                );
              },
              child: const Text('Go Search Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Or instead we can launch the bottom navigation page(with shell) for different tab with only changing the path',
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class SearchPage extends StatelessWidget {
  const SearchPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Search"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.go(
                  CustomNavigationHelper.homePath
                );
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.detailPath
                );
              },
              child: const Text('Go Home Tab -> Push Detail Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'It will change the tab without loosing the state',
                textAlign: TextAlign.center,
              ),
            ),
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.go(
                  CustomNavigationHelper.settingsPath,
                );
              },
              child: const Text('Go Settings Tab'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Or instead we can launch the bottom navigation page(with shell) for different tab with only changing the path',
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class SettingsPage extends StatelessWidget {
  const SettingsPage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text("Settings"),
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.go(
                  CustomNavigationHelper.signInPath,
                );
              },
              child: const Text('Go SignIn Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Or instead we can launch the bottom navigation page(with shell) for different tab with only changing the path',
                textAlign: TextAlign.center,
              ),
            ),
            ElevatedButton(
              onPressed: () {
                CustomNavigationHelper.router.push(
                  CustomNavigationHelper.signUpPath,
                );
              },
              child: const Text('Push SignIn Page'),
            ),
            const Padding(
              padding: EdgeInsets.all(8.0),
              child: Text(
                'Or instead we can launch the bottom navigation page(with shell) for different tab with only changing the path',
                textAlign: TextAlign.center,
              ),
            ),
          ],
        ),
      ),
    );
  }
}

class BottomNavigationPage extends StatefulWidget {
  const BottomNavigationPage({
    super.key,
    required this.child,
  });

  final StatefulNavigationShell child;

  @override
  State<BottomNavigationPage> createState() => _BottomNavigationPageState();
}

class _BottomNavigationPageState extends State<BottomNavigationPage> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Bottom Navigator Shell'),
      ),
      body: SafeArea(
        child: widget.child,
      ),
      bottomNavigationBar: BottomNavigationBar(
        type: BottomNavigationBarType.fixed,
        currentIndex: widget.child.currentIndex,
        onTap: (index) {
          widget.child.goBranch(
            index,
            initialLocation: index == widget.child.currentIndex,
          );
          setState(() {});
        },
        items: const [
          BottomNavigationBarItem(
            icon: Icon(Icons.home),
            label: 'home',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.search),
            label: 'search',
          ),
          BottomNavigationBarItem(
            icon: Icon(Icons.settings),
            label: 'settings',
          ),
        ],
      ),
    );
  }
}

```
                                                          

# Firebase-Notification-Wrapper
