---
title: "Deploy the Moor database on Flutter!"
description: ""
image: "images/post/moor_database_flutter.png"
date: 2020-12-30T18:19:25+06:00
categories: ["Flutter","Development", "Database"]
type: "Flutter" # available types: [featured/regular]
draft: false
---
There are different databases to incorporate into a **Flutter** application, each with its advantages and disadvantages. In this article, along with a short introduction, we are going to specifically look at a database to work with in Flutter: [Moor](https://pub.dev/packages/moor). So go ahead and deploy the Moor database on Flutter!

The introduction to Moor will be done by developing a small project in Flutter, which you can download from [GitHub](https://github.com/raulferrerdev/moor_project).

{{< image src="images/post/moor_database_flutter_1.png">}}

#### Introduction

When we begin to develop applications, there comes a time when we find ourselves with the need to save data within the application itself: information about the user, generated data.

At that point, what we do is introduce a database in the development of the application. But what database do we choose to work with?
#### Types of databases

Today there are numerous databases that can be used in mobile development, but they can be basically classified into two groups: **Relational** and **NoSQL**.
##### Relational databases

In relational databases, information is stored in tables (with rows and columns, where each row represents a record and each column an ​​attribute of that record). In addition to the saved data, this type of database also saves the relationships between them.

For example, SQLite is an example of a relational database, which we can implement in Flutter using the [SQFLite](https://pub.dev/packages/sqflite) package.
##### NoSQL databases

NoSQL databases do not have predefined schemas like relational databases would. A clear example are databases such as [MongoDB](https://pub.dev/packages/mongo_dart), which store information in the form of documents and allow you to easily work with unstructured information.
#### What is Moor?

[Moor](https://pub.dev/packages/moor) is a library for Flutter that allows us to work with the SQLite database easily and in Dart. Moor works using SQFLite and, what Moor does for us is to transform our Dart code into SQL language (although it also allows us to use SQL).
##### How to install Moor in our project?

The first step to using Moor in our projects is to install a series of dependencies (updated). First we create a project by entering the following command in the terminal:
{{< highlight shell  "linenos=inline,linenostart=1" >}}

flutter create moor_project
{{< / highlight >}}


Then we access the pubspec.yaml file (for example, through Visual Studio Code) and include the dependencies:
{{< highlight shell  "linenos=inline,linenostart=1" >}}

dependencies:
  flutter:
    sdk: flutter
  get_it: ^5.0.3
  moor_flutter: ^3.1.0
  path_provider: ^1.6.24
 
dev_dependencies:
  flutter_test:
    sdk: flutter
  moor_generator: ^3.4.0
  build_runner: ^1.10.4
{{< / highlight >}}


* *moor_flutter*. This bookstore is the heart of Moor.
* *moor_generator*. This library is in charge of transforming the classes with which we create the database tables in database language.
* *build_runner*. This library generates the database files in Dart language.

We also add the [path_provider](https://pub.dev/packages/path_provider) library, which will allow us to find and add the database to the application sandbox, and the [get_it](https://pub.dev/packages/get_it) library, which is a service provider and will help us establish the AppDatabase class as a singleton.
#### Our first table with Moor

One of the advantages of Moor is that when creating a table we can do it directly in Dart, without using SQL language. Suppose we want our database to contain a user table.

By using Moor, we will create a class (in the *users.dart* file), which we can call Users, and which will extend from the Table class:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'package:moor/moor.dart';
 
class Users extends Table {
  IntColumn get id => integer().autoIncrement()();
  TextColumn get username => text()();
  TextColumn get mail => text()();
  TextColumn get country => text()();
  TextColumn get language => text()();
}
{{< / highlight >}}

By adding the autoIncrement feature to the id parameter we achieve two things: the first is that this parameter increases by 1 each time we add a user; the second is that at that time, the id parameter becomes the primaryKey of the table. As you can see, the Users class will be our table in the database, while the properties defined as getters will be the columns of the table. Moor accepts the following types of data:
{{< image src="images/post/moor_database_flutter_2.png">}}

#### Creation of the database

Now, what we must do is create the database, and that it takes into account the Users table. We do this with the following code, which we will introduce in the database_manager.dart class:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'dart:io';
 
import 'package:moor_project/database/users.dart';
import 'package:moor/moor.dart';
import 'package:moor/ffi.dart';
import 'package:path_provider/path_provider.dart';
import 'package:path/path.dart' as p;
 
part 'database_manager.g.dart';
 
LazyDatabase openConnection() {
  return LazyDatabase(() async {
    final dbFolder = await getApplicationDocumentsDirectory();
    final file = File(p.join(dbFolder.path, 'db.sqlite'));
    return VmDatabase(file);
  });
}
 
@UseMoor(tables: [Users])
class AppDatabase extends _$AppDatabase {
  AppDatabase(QueryExecutor e) : super(e);
 
  @override
  int get schemaVersion => 1;
}
{{< / highlight >}}


This code contains two different parts:

* In the first part, we create the db.sqlite file that will be the one that saves the data in the application’s sandbox (from the documents directory that we obtain through the getApplicationDocumentsDirectory() instruction).
* The second part what it does is run the database. To do this, in our application we simply have to make the call AppDatabase(openConnection()).

We have also added the instruction part ‘database_manager.g.dart’;, which will contain the generated code.
#### Code generation

Now we simply have to make the code generation process run (in the database_manager.g.dart file that we have defined). To do this, we go to the terminal and execute the following code:
{{< highlight shell  "linenos=inline,linenostart=1" >}}

flutter pub run build_runner build watch
{{< / highlight >}}
{{< image src="images/post/moor_database_flutter_3.png">}}

#### Time to add queries

Once we have created the database with the users table, what we need is to add the queries that will allow us to create, update, delete … those users.

These queries can be added directly in the AppDatabase class that we created earlier:
{{< highlight dart  "linenos=inline,linenostart=1" >}}
@UseMoor(tables: [Users])
class AppDatabase extends _$AppDatabase {
  AppDatabase(QueryExecutor e) : super(e);
 
  @override
  int get schemaVersion => 1;
 
  // Queries
  // Get all users
  Future<List<User>> getAllUsers() => select(users).get();
 
  // Add user
  Future insertUser(User user) => into(users).insert(user);
 
  // Update user
  Future updateUser(User user) => update(users).replace(user);
 
  // Delete user
  Future deleteUser(User user) => delete(users).delete(user);
}
{{< / highlight >}}

Keep in mind that in the case of updating an object in the table, this is done using the primaryKey of said object (in our case it is the id parameter, which was marked as primaryKey when adding the autoIncrement option).
#### It’s flutter time

Now that we have the project created and we have included the database_manager.dart and users.dart classes, we can continue creating the user interface that will allow us to create a user.
##### Adding the service provider

In order to establish the AppDatabase class as a singleton, we will use the get_it library, which is a service provider and will allow us to access the database from anywhere in the application.

We will do this by creating the service_locator.dart class, in which we will introduce the following code:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'package:get_it/get_it.dart';
import 'package:moor_project/database/database_manager.dart';
 
GetIt locator = GetIt.instance;
 
Future setupLocator() async {
  locator.registerSingleton(AppDatabase(openConnection()));
}
{{< / highlight >}}


What setupLocator() allows us to do is initialize the database and register it as a singleton.
#### Starting the application

To begin, we will take the main.dart class that was created when the project was generated, we will delete all its content and we will add the following code:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'package:flutter/material.dart';
import 'package:moor_project/service_locator.dart';
 
import 'pages/home.dart';
 
Future<void> main() async {
  // Register the database service before the app starts
  await setupLocator();
 
  runApp(MyApp());
}
 
class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'User Database',
      theme: ThemeData(
        primarySwatch: Colors.blue,
      ),
      home: Home(),
    );
  }
}
{{< / highlight >}}


We have modified the call to main() to be asynchronous, since the setupLocator() call returns a Future: when this call finishes executing, the rest of the application will be executed.
#### The data entry screen

Now we will create a simple screen to enter the data with which we will create the user in the database. To do this, we create a home.dart file and add the following code:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'package:flutter/material.dart';
import 'package:moor_project/database/database_manager.dart';
import 'package:moor_project/service_locator.dart';
import 'package:moor_project/widgets/custom_textfield.dart';
 
class Home extends StatefulWidget {
  Home({Key key}) : super(key: key);
 
  @override
  _HomeState createState() => _HomeState();
}
 
class _HomeState extends State<Home> {
  final usernameController = TextEditingController();
  final mailController = TextEditingController();
  final countryController = TextEditingController();
  final languageController = TextEditingController();
 
  @override
  void dispose() {
    usernameController.dispose();
    mailController.dispose();
    countryController.dispose();
    languageController.dispose();
 
    super.dispose();
  }
 
  void createUser(
      {String username, String mail, String country, String language}) async {
    User user = User(
        id: null,
        username: username,
        mail: mail,
        country: country,
        language: language);
 
    await locator<AppDatabase>().insertUser(user);
  }
 
  @override
  Widget build(BuildContext context) {
    final _username = Padding(
        padding:
            EdgeInsets.only(top: 0.0, bottom: 0.0, left: 30.0, right: 30.0),
        child: CustomTextField(
          hintText: 'Username',
          controller: usernameController,
        ));
 
    final _mail = Padding(
        padding:
            EdgeInsets.only(top: 20.0, bottom: 0.0, left: 30.0, right: 30.0),
        child: CustomTextField(
          hintText: 'Mail',
          controller: mailController,
        ));
 
    final _country = Padding(
        padding:
            EdgeInsets.only(top: 20.0, bottom: 0.0, left: 30.0, right: 30.0),
        child: CustomTextField(
          hintText: 'Country',
          controller: countryController,
        ));
 
    final _language = Padding(
        padding:
            EdgeInsets.only(top: 20.0, bottom: 0.0, left: 30.0, right: 30.0),
        child: CustomTextField(
          hintText: 'Language',
          controller: languageController,
        ));
 
    final _createButton = Padding(
      padding: EdgeInsets.only(top: 50.0, bottom: 0.0, right: 50.0, left: 50.0),
      child: RaisedButton(
        onPressed: () => createUser(
            username: usernameController.text,
            mail: mailController.text,
            country: countryController.text,
            language: languageController.text),
        child: const Text('Add User', style: TextStyle(fontSize: 20)),
      ),
    );
 
    final _screen = Material(
        color: Colors.transparent,
        child: new Container(
          child: new Column(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [_username, _mail, _country, _language, _createButton]),
        ));
 
    return Scaffold(
      backgroundColor: Colors.purple,
      body: Container(
        width: double.infinity,
        height: double.infinity,
        child: SingleChildScrollView(
          child: ConstrainedBox(
            constraints:
                BoxConstraints(minHeight: MediaQuery.of(context).size.height),
            child: Center(
              child: _screen,
            ),
          ),
        ),
      ),
    );
  }
}
{{< / highlight >}}


It is a StatefulWidget to which we add four CustomTexField widgets and a RaisedButton widget. The CustomTextField widget is a component that we created separately to customize a TextField type widget and, on the other hand, to reuse code.

For this reason we create the custom_textfield.dart file and add the following code:
{{< highlight dart  "linenos=inline,linenostart=1" >}}

import 'dart:ui';
import 'package:flutter/material.dart';
  
class CustomTextField extends StatelessWidget {
  const CustomTextField({
    Key key,
    this.hintText,
    this.controller,
  }) : super(key: key);
 
  final String hintText;
  final TextEditingController controller;
 
  @override
  Widget build(BuildContext context) {
    const _textStyle = TextStyle(
      color: Color(0xFF343434),
      fontWeight: FontWeight.w300,
      fontSize: 16.0,
    );
 
    return TextField(
      controller: this.controller,
      cursorColor: Color(0xFF343434),
      style: _textStyle,
      decoration: InputDecoration(
        contentPadding: EdgeInsets.only(left: 7.0, right: 7.0),
        filled: true,
        fillColor: Colors.white,
        border: InputBorder.none,
        hintText: this.hintText,
        hintStyle: _textStyle,
      ),
      textAlign: TextAlign.center,
    );
  }
}
{{< / highlight >}}
#### Launching the application

With all the code in place, we are going to execute the code in an iOS simulator. To do this, first we load the simulator (from the Simulator application) and then, in the terminal, from the root directory of the project we execute:
{{< highlight shell  "linenos=inline,linenostart=1" >}}

flutter run
{{< / highlight >}}


After the execution process, the simulator shows the following screen:
{{< image src="images/post/moor_database_flutter_4.png">}}

We introduce the different data:

{{< image src="images/post/moor_database_flutter_5.png">}}

And we press the ‘Add User‘ button.

It is a simple application, in which we have not added any other screen to verify the saved data. But to check that everything has gone well, we simply have to do a search for the db.sqlite file in the Library > Developer > CoreSimulator > Devices directory … (if it is macOSX).

Once the file is found, we can open it with an application like [DB Browser for SQLite](https://sqlitebrowser.org/), and we can see how the data has been saved.
{{< image src="images/post/moor_database_flutter_6.png">}}

#### Conclusions

In this article I wanted to make a brief introduction to the use of databases in projects developed with Flutter and, especially, to the use of the Moor database. This database has many more possibilities that we will see in later articles.
