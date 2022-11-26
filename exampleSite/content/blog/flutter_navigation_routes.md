---
title: "Improve your Flutter Navigation with Routes"
description: ""
image: "images/post/flutter_navigation_routes.png"
date: 2020-03-08T18:19:25+06:00
categories: ["Flutter"]
tags: "Flutter"
draft: false
---
In all applications we need to navigate from one screen to another to be able to access their contents. This can be done in two different ways in Flutter: by creating an instance of the *PageRoute* widget and then passing it to the **Navigator** widget, or by using the Named Routes option. In this article we are going to see how to set **Flutter** navigation with **Routes**.
#### Using *PageRoute* to navigate between pages

Both the *Cupertino* package (iOS layout) and the *Material Design* (Android layout), present a display router: *CupertinoPageRoute* and *MaterialPageRoute*.

For example, let’s say we have an application with a screen called *Home*, and we want to navigate to another screen called *Settings*. To do so, for example with *MaterialPageRoute*, the code to use would be the following:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
Navigator.push(context, new MaterialPageRoute(
  builder: (context) => Settings()
));
{{< / highlight >}}

What we are doing is using the *Flutter Navigator* widget to which we pass the current context (*BuildContext*), along with the indication of where we want to navigate.

The *Flutter Navigator*‘s function is to manage a series of secondary widgtets according to a stack structure, in the same way that other types of platforms do.

In this case that we have just seen, the movement between the different pages is carried out by the **Navigator** according to a logic of *push* (*Navigator.push*) and *pop* (*Navigator.pop*), the latter being the one that would allow us to return to a page:
{{< highlight swift  "linenos=inline" >}}
 Navigator.pop(context);
{{< / highlight >}}

#### Using named routes

The navigation system is fine if we have a simple application. However, in more complex applications where it is necessary to navigate to a page from different points, we can find duplicate code.

To avoid this we can use named routes. For this, suppose the case in which we have an application with four different screens and their corresponding files:

* Welcome: welcome_screen.dart
* Settings: settings_screen.dart
* Dashboard: dashboard_screen.dart
* Library: library_screen.dart

We can introduce this information in the routes parameter of the *MaterialApp* class:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

import 'package:flutter/material.dart';
import './screens/welcome_screen.dart';
import './screens/settings_screen.dart';
import './screens/dashboard_screen.dart';
import './screens/library_screen.dart';

void main() => runApp(NavDemoApp());

class NavDemoApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter navigation demo app',
      home: AccessPage(),
    );
  }
}

class AccessPage extends StatefulWidget {
  @override
  _AccessPageState createState() => _AccessPageState();
}

class _AccessPageState extends State&lt;AccessPage> {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
       home: WelcomeScreen(),
       routes: {
        'welcome_screen': (context) => WelcomeScreen(),
        'settings_screen': (context) => SettingsScreen(),
        'dashboard_screen': (context) => DashboardScreen(),
        'library_screen': (context) => LibraryScreen()
    });
  }
}
{{< / highlight >}}

With the addition of these routes we can navigate from one page to another using the following code:
{{< highlight swift  "linenos=inline" >}}
Navigator.pushNamed(context, 'dashboard_screen');
{{< / highlight >}}


To avoid the use of text strings every time we have to make the call (thus reducing possible typographical errors), we can add the name of each route within each of the screens:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

import 'package:flutter/material.dart';

class DashboardScreen extends StatelessWidget {
  static const String id = 'dashboard_screen';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text('Dashboard Screen'),
      ),
    );
  }
}
{{< / highlight >}}


In this way we can change the code in the *_AccessPageState* class:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class _AccessPageState extends State&lt;AccessPage> {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
       home: WelcomeScreen(),
       routes: {
        Welkome.id: (context) => WelcomeScreen(),
        Settings.id: (context) => SettingsScreen(),
        Dashboard.id: (context) => DashboardScreen(),
        Library.id: (context) => LibraryScreen()
    });
  }
}
{{< / highlight >}}


And make the call by:
{{< highlight swift  "linenos=inline" >}}
Navigator.of(context).pushNamed(Welcome.id);
1
{{< / highlight >}}

#### Search order of routes

When searching for routes, the **Navigator** component follows the following order:

* The default path is the one indicated in the home parameter (in our case we have indicated *WelcomeScreen*).
* Otherwise, the routes table indicated in the *routes* parameter (in which we have indicated four routes) is used.
* Then the *onGenerateRoute* parameter would be called (it must return a non-null value for any route not contemplated in home or in routes).
* If all of the above fail, *onUnknownRoute* is called.

#### Using *onGenerateRoute* for navigation

We can also plan the navigation of our application by configuring the *onGenerateRoute* parameter. This parameter must be passed a function that takes a parameter of type *RouteSettings* and returns one of type *Route*.

To do this we create the *MyRoutes* class, which will help us with this purpose:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

class MyRoutes {
  static const String ROUTE_WELCOME = "/welcome";
  static const String ROUTE_SETTINGS = "/settings";
  static const String ROUTE_DASHBOARD = "/dashboard";
  static const String ROUTE_LIBRARY = "/library";

static Route&lt;dynamic> generateRoute(RouteSettings settings) {
  switch (settings.name) {
    case '/welcome':
      return MaterialPageRoute(builder: (_) => WelcomeScreen());
    case '/settings':
      return MaterialPageRoute(builder: (_) => SettingsScreen());
    case '/dashboard':
      return MaterialPageRoute(builder: (_) => DashboardScreen());
    case '/library':
      return MaterialPageRoute(builder: (_) => LibraryScreen());
    default:
      return MaterialPageRoute(builder: (_) => WelcomeScreen());
    }
  }
}
{{< / highlight >}}


If we use *onGenerateRoute*, the *NavDemoApp* class is shown as follows:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class NavDemoApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      onGenerateRoute: MyRoutes.generateRoute,
      initialRoute: ROUTE_WELCOME,
    );
  }
}
{{< / highlight >}}


And the navigation is done as follows:
{{< highlight swift  "linenos=inline " >}}
Navigator.pushNamed(context, ROUTE_LIBRARY);
{{< / highlight >}}

#### Conclusions

We have seen how we can implement navigation between screens in a *Flutter* project directly (creating a *PageRoute* instance) or by using routes (both in the routes parameter and in the *onGenerateRoute* parameter):