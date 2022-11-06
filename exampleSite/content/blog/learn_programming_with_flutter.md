---
title: "Do you want to learn to program with Flutter? Get ready!"
description: ""
image: "images/post/learn_programming_with_flutter_intro.jpg"
date: 2019-12-28T18:19:25+06:00
categories: ["Dart","Development", "Flutter"]
type: "regular" # available types: [featured/regular]
draft: false
---
In mobile application development, along with native development platforms (iOS and Android), there are others, called hybrids, that allow developing applications for multiple platforms while maintaining a common code base: *PhoneGap*, *Ionic*, *React Native*, *Xamarin* and from recently, **Flutter**. Do you want to learn to program with Flutter? Here we will take the first steps.

#### Let’s see what Flutter is
# ![Flutter Logo](https://raulferrergarcia.netlify.app/images/post/learn_programming_with_flutter_1.png)
[Flutter](https://flutter.dev/) is an open source SDK, originating around 2015, but not officially released by Google until December 2018 released in 2017 by Google. Flutter uses Dart as a programming language, plus C++ and Skia as rendering engines.
#### The Dart programming language
Dart logo

[Dart](https://dart.dev/) is an open source language, the first stable version of which was released in 2011. The idea of Dart is to facilitate its use by developers, so it has a very wide series of tools and utilities integrated: its own dependency manager, compilers … and, in addition, it presents a syntax similar to Javascript or Java.

It is a strongly typed and object-oriented. From a mobile application point of view, Dart supports JIT (Just In Time) compilation, which allows **Flutter** to recompile directly while the application is running (Hot Reload), and AOT (Ahead Of Time), which allows compilation in native code.
#### Journey Inside Flutter: The Widgets

In **Flutter** we can say that everything works around **Widgets**.

A **Widget** is a component that helps us to define and build the application interface (it would be something like the UIView in iOs, or the Views in Android). In Flutter everything is a Widget, everything that allows us to build the application (we can even consider the application to be a widget).

In **Flutter** you can create **Widgets** with styles that reproduce those of iOS (using the *Cupertino* library) or Android (using the *Material Design* library), but we can also create our own.
#### Do I choose Flutter? Pros and cons of Flutter

As in everything, Flutter has a series of advantages and disadvantages that you have to take into account when using it. If you want to learn to program with Flutter, you should know what these pros and cons are.
##### Pros

* By having the same code base for iOS and Android, it allows for **relatively fast development**.
* The Hot Reload allows us to **instantly see (normally) the changes made**, since it is not necessary to recompile the code.
* Flutter, apart from iOS and Android, also allows compiling for the web (update). It is **perfect for developing MVP** (Minimum Viable Product) with just the right features.
* It has a **fairly fast learning curve** and has a growing developer community.

##### Cons

* Flutter is fairly new and therefore **immature**. Dart is also immature when compared to Swift or Kotlin.
* The fact that both Flutter and Dart are in **continuous development** (and with changes that happen quickly), **makes it difficult to maintain the code in the long term**.
* **Flutter does not yet take advantage of all the capabilities of the operating systems** for which it compiles the applications (which can be done in native development), with many libraries still in development.
* Although there are libraries (Cupertino or Material Design) that reproduce the style of native applications, they are not 100% the same as these.
* Many of the functionalities that are achieved natively in iOS or Android, are achieved in Flutter thanks to **third-party libraries, which increases the dependency on development**. In addition, **the number of these libraries is still relatively low**, so we may find ourselves having to develop our own library (which will increase development time).
* Compared to native applications, Flutter applications are larger (**take up more space**) and when run, they **tend to consume more device memory**.
* A final note to keep in mind: it would **not be the first time that Google has closed a project**, which makes the future of Flutter somewhat uncertain.

#### We are going to install Flutter on the computer

Let’s see how we can install **Flutter** on a macOS operating system:

* First we download the [latest stable package from Flutter](https://flutter.dev/docs/get-started/install/macos).
* We move it to the folder in which we want to unzip it and unzip it.
* If we don’t want to download the folder, we can install it via git:

{{< highlight swift>}}
 git clone https://github.com/flutter/flutter.git
{{< / highlight >}}

* The next step is to add **Flutter** to the System Path, using:

{{< highlight swift>}}
 export PATH="$PATH:[PATH_TO_FLUTTER_DIRECTORY]/flutter/bin"
{{< / highlight >}}


* We verify the directory with:

{{< highlight swift>}}
 echo $PATH
{{< / highlight >}}


* And we check that it works with:

{{< highlight swift>}}
 which flutter
{{< / highlight >}}


#### What else do I need besides Flutter?

If we want to develop, compile and test applications on iOS and Android, we need to have the respective SDKs installed (**Xcode** and **Android Studio**). To know what we need to install on our computer we execute the following command in the terminal:

{{< highlight swift>}}
 flutter doctor
{{< / highlight >}}


As you can see, in this case everything related to Android development would be missing.
flutter doctor
Result of the flutter doctor command.
#### Our first app on Flutter

To create our first application in fluter we simply have to go to the directory where we want to create it:

{{< highlight swift>}}
 cd Workspace/Flutter
{{< / highlight >}}


And we write:
{{< highlight swift>}}
 flutter create my_first_flutter_app
{{< / highlight >}}


Where ‘my_first_flutter_app’ you can change it to the name you want to give the application.

Once this is done, flutter launches a process of creating the application’s file and file structure.

If you now access the directory you will see something similar to the following:
Flutter app folderFolder and file structure (updated).

We can see specific folders for Android and iOS (with the specific files for each of these platforms). Update, the web folder shows us the content if we compile the app to be shown as web.
#### And we start the application!

Now we are going to launch the application that we have created. To do this, if you are using Xcode, start the simulator and then, from within the application folder, execute:
{{< highlight swift>}}
 flutter run
{{< / highlight >}}

Flutter will start the process of compiling, linking and signing the app, and it will run it in the simulator:


#### Conclusions

Do you want to learn to program with Flutter? In this article we have taken our first steps with Flutter. We have seen what Flutter is, how it works, what are its advantages and disadvantages and how to create our first application with Flutter.