---
title: "Learn how to build in Flutter: Widgets"
description: ""
image: "images/post/learn_how_build_widgets_flutter.png"
date: 2020-01-12T18:19:25+06:00
categories: ["Flutter","Development"]
type: "regular" # available types: [featured/regular]
draft: false
---
In a [previous article](https://raulferrergarcia.netlify.app/blog/learn_programmming_with_flutter/), in which I introduce Flutter, I discussed the importance of widgets in Flutter. Let’s see how it is built in Flutter.
### What is a widget?

We can think of **widgets** as reusable code blocks that describe the appearance of the user interface (UI). They do this based on their state, which, as we will see later, can be *static* (does not change) or *dynamic* (changes over time).

A **widget** can not only be a button, a text, an image … something that we would associate directly with a graphic appearance, but it is also everything that allows defining that appearance, such as the margin of an object or that we can center it in the interface .

Thus, while *Image*, *Text* or *FlatButton* are widgets that allow us to display an image, a text or a button, they are also *Padding* widgets (that allow us to add space around another widget) or *FutureBuilder* (that allow us to add an asynchrony pattern) .
### Types of Widgets

To know how to build in Flutter, you have to know that there are two types of widgets, **StatelessWidgets** and **StatefulWidgtets**.
#### Stateless Widgets

They are called stateless or static widgets. These are widgets that do not save any type of state, that is, they do not have values that can change. For example, an Image widget that only displays an image on the screen.
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class Car extends StatelessWidget {
  const car({ Key key }) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Container(child: Text("Red car"));
  }
}
{{< / highlight >}}


As you can see, the declaration of a Stateless widget is done by creating a class (*Car*) and making it extend from *StatelessWidget* (extends *StatelessWidget*). Inside we see that there is a build method that builds the widget (in this case it contains a *Container* widget that contains another *Text* widget).
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class Car extends StatefulWidget {
  const Car({
    Key key,
    this.color,
    this.brand,
  }) : super(key: key);

  final String color;
  final String brand;

  _CarState createState() => _CarState();
}

class _CarState extends State<Car> {
  double _speed = 0.0;

  void speedUp() {
    setState(() { _speed += 1.0; });
  }

  @override
  Widget build(BuildContext context) {
    return Container(
      color: widget.color,
      child: Column(
        children: <Widget>[
           Text(widget.brand),
           Text(widget.speed.toString()
        ]
      ),
    );
  }
}
{{< / highlight >}}

In this case, we declare the *Car* class and extend it from *StatefulWidget*. Inside, we define two variables (*color* and *brand*), which will be the parameters that we will have to pass to the class when we instantiate it from another point of the application.

In the second part of the code, we are defining the state of the *Car* class inside which we can see, on the one hand, the *speedUp*() function, that each time it is called it will increase the value of one of the speed parameter. In addition, this is done within the *setState* call, with which we modify the state of the class and, at the same time, a new rendering of the widget is executed.

Next you can see the build function, made up of a *Container* type widget, which in turn contains a *Column* type widget, which contains two *Text* type children.
### How are widgets structured? The widget tree

We have just seen an example of a widget that contains different nested widgets, following a parent-child structure. This case is quite simple, but as the design of an interface becomes more complicated, this structure has become longer and more complex. This is what is known as a **widget tree**.

If you have read my [previous article](https://raulferrergarcia.netlify.app/blog/learn_programmming_with_flutter/) on Flutter and have created your first application, you will have seen in the example code (I recommend *Visual Studio Code* for this) the following block:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);

  // This widget is the home page of your application. It is stateful, meaning
  // that it has a State object (defined below) that contains fields that affect
  // how it looks.

  // This class is the configuration for the state. It holds the values (in this
  // case the title) provided by the parent (in this case the App widget) and
  // used by the build method of the State. Fields in a Widget subclass are
  // always marked "final".

  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      // This call to setState tells the Flutter framework that something has
      // changed in this State, which causes it to rerun the build method below
      // so that the display can reflect the updated values. If we changed
      // _counter without calling setState(), then the build method would not be
      // called again, and so nothing would appear to happen.
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    // This method is rerun every time setState is called, for instance as done
    // by the _incrementCounter method above.
    //
    // The Flutter framework has been optimized to make rerunning build methods
    // fast, so that you can just rebuild anything that needs updating rather
    // than having to individually change instances of widgets.
    return Scaffold(
      appBar: AppBar(
        // Here we take the value from the MyHomePage object that was created by
        // the App.build method, and use it to set our appbar title.
        title: Text(widget.title),
      ),
      body: Center(
        // Center is a layout widget. It takes a single child and positions it
        // in the middle of the parent.
        child: Column(
          // Column is also a layout widget. It takes a list of children and
          // arranges them vertically. By default, it sizes itself to fit its
          // children horizontally, and tries to be as tall as its parent.
          //
          // Invoke "debug painting" (press "p" in the console, choose the
          // "Toggle Debug Paint" action from the Flutter Inspector in Android
          // Studio, or the "Toggle Debug Paint" command in Visual Studio Code)
          // to see the wireframe for each widget.
          //
          // Column has various properties to control how it sizes itself and
          // how it positions its children. Here we use mainAxisAlignment to
          // center the children vertically; the main axis here is the vertical
          // axis because Columns are vertical (the cross axis would be
          // horizontal).
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'You have pushed the button this many times:',
            ),
            Text(
              '$_counter',
              style: Theme.of(context).textTheme.headline4,
            ),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: Icon(Icons.add),
      ), // This trailing comma makes auto-formatting nicer for build methods.
    );
  }
}
{{< / highlight >}}

In this case, the build method returns a somewhat more complex series of widgets:
{{< image src="images/post/learn_how_build_widgets_flutter_1.png">}}

* **Scaffold:** is in charge of implementing the basic structure of the visual template.
* **AppBar:** displays a bar at the top of the application.
* **Center:** it is a layout type widget (design), which allows the content to be centered on its parent.
* **Column:** it is also a layout type widget, which allows a list of widgets to be arranged vertically.
* **Text:** display text on the screen.
* **FloatingActionButton:** This widget features an on-screen button.

### Conclusions

We have seen the two categories in which we can divide the widgets in Flutter: *Stateless* and *Stateful*. The use of each type will depend on its use. In general we can say that, if it does not change, we will use a **StatelessWidget** and, if it changes, we will use a **StatefulWidget**.