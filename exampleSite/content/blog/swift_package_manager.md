---
title: "How to use Swift Package Manager"
description: ""
image: "images/post/swift_package_manager.png"
date: 2020-04-13T18:19:25+06:00
categories: ["Swift", "SPM"]
tags: "Swift"
draft: false
---
**Swift Package Manager** (SPM) was the dependency manager for Swift that Apple released together with Swift in 2015. Swift Package Manager joined other well-known dependency managers, such as [CocoaPods](https://cocoapods.org/) or [Carthage](https://github.com/Carthage/Carthage). However, it was not until the publication of Xcode 11 that this tool was integrated into Xcode itself, which made it easier to use. Now we are going to learn how to use Swift Package Manager.
#### A Swift package

A Swift package is made up of a series of files with the source code and a manifest, a file called Package.swift (found in the root directory), which defines the name of the package, its contents, etc. When we compile these files, we generate a separate module that can then be shared (for example, from GitHub) imported into other projects. These files are compiled to form a module that can then be imported by other projects.
#### How can we create a Swift package

With Xcode 11, it is very easy to create a Swift package. To do this, in the Xcode menu select File > New > Swift Package…
{{< image src="images/post/swift_package_manager_1.png">}}


Then we indicate the name that we are going to give the project and create it:
{{< image src="images/post/swift_package_manager_2.png">}}


In this simple way we have generated a Swift package. The first thing we see on the screen is the contents of the Package.swift file:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

import PackageDescription

let package = Package(
    name: "MyLibrary",
    products: [
        // Products define the executables and libraries produced by a package, and make them visible to other packages.
        .library(
            name: "MyLibrary",
            targets: ["MyLibrary"]),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        // .package(url: /* package url */, from: "1.0.0"),
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "MyLibrary",
            dependencies: []),
        .testTarget(
            name: "MyLibraryTests",
            dependencies: ["MyLibrary"]),
    ]
)
{{< / highlight >}}


In this manifesto we find:

* **name**. It is the name of the project.
* **products**. Defines which executables and libraries defined in the project are visible to other packages.
* **dependencies**. Here are defined other packages on which our project depends.
* **targets**. It is where we add the new dependencies to our main project.

#### Example of adding dependencies

For example, suppose we want to create a Swift package and we know that we are going to use the RxSwift library. What we do first is access the RxSwift GitHub repository. If we go down to the point where it indicates how it can be installed, we will see that it gives us the options of Cocoapods, Carthage and Swift Package Manager. The latter is the one that interests us and, in which we observe what dependency and what target we have to add to our Swift package:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
let package = Package(
  name: "RxTestProject",
  dependencies: [
    .package(url: "https://github.com/ReactiveX/RxSwift.git", from: "5.0.0")
  ],
  targets: [
    .target(name: "RxTestProject", dependencies: ["RxSwift", "RxCocoa"])
  ]
)
{{< / highlight >}}


Therefore, in our Package.swift we add these values, with what remains as follows:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

import PackageDescription

let package = Package(
    name: "MyLibrary",
    products: [
        // Products define the executables and libraries produced by a package, and make them visible to other packages.
        .library(
            name: "MyLibrary",
            targets: ["MyLibrary"]),
    ],
    dependencies: [
        // Dependencies declare other packages that this package depends on.
        .package(url: "https://github.com/ReactiveX/RxSwift.git", from: "5.0.0")
    ],
    targets: [
        // Targets are the basic building blocks of a package. A target can define a module or a test suite.
        // Targets can depend on other targets in this package, and on products in packages which this package depends on.
        .target(
            name: "MyLibrary",
            dependencies: ["RxSwift", "RxCocoa"]),
        .testTarget(
            name: "MyLibraryTests",
            dependencies: ["MyLibrary"]),
    ]
)
{{< / highlight >}}


The value from: “5.0.0” tells us which version of RxSwift will be included in our project. That is, the latest version of RxSwift that is equal to or greater than 5.0.0 will be included. However, we can also indicate that only a certain version, a branch or even a revision is included:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

// Version of a package
.package (url: "https://github.com/ReactiveX/RxSwift.git", .exact ("5.1.0"))

// Version of a certain branch (for example, develop)
.package (url: "https://github.com/ReactiveX/RxSwift.git", .branch ("develop"))

// Version of a certain revision (commit):
.package (url: "https://github.com/ReactiveX/RxSwift.git", .revision ("002d325b0bdee94e7882e1114af5ff4fe1e96afa"))
{{< / highlight >}}

Once the dependency has been added to our project, when we build it, we will see that a new section (Swift Package Dependencies) appears in the side navigation menu, indicating the dependencies that we have added to our project (in this case, RxSwift).
{{< image src="images/post/swift_package_manager_3.png">}}
#### Platform settings

In the Package.swift file, we can also indicate for which platform or operating system our project is specified. For example, if we want that our Swift package can only be used for iOS 12+ operating systems, what we will do is indicate it as follows:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
let package = Package(
    name: "MyLibrary",
    platforms: [.iOS(.v12)],
    products: [
      ...
{{< / highlight >}}


The different supported platforms, as shown in the [Apple documentation](https://developer.apple.com/documentation/swift_packages/platform) are:

* iOS
* linux
* macOS
* tvOS
* watchOS

We can also define more than one platform. For example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let package = Package(
    name: "MyLibrary",
    platforms: [.iOS(.v12), .macOS(.v10_15)],
    products: [
      ...
{{< / highlight >}}

#### We add code to our Swift package

Once we have configured our project, we can add code. We must remember that we must declare as public those classes and methods that we want to use in the projects in which we import the Swift package. Once we have finished the project and have uploaded it to a repository (such as GitHub or GitLab), we can now download it and include it in other projects.
#### How to add a Swift package to our application

To add the Swift package that we have created to an application, we simply do the following (as long as we are using Xcode 11):

* From the top menu we access File > Swift Packages > Add Package Dependency…

{{< image src="images/post/swift_package_manager_4.png">}}

* In the window that appears, we add the project URL and press ok (this will download the Swift package to our project.):

{{< image src="images/post/swift_package_manager_5.png">}}

Wherever we want to use it, we simply have to import them:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
#import MyLibray
{{< / highlight >}}

#### Conclusion

With the addition of Swift Package Manager to Xcode 11, it has become very easy to create and add dependencies to our projects. This opens a new way of, for example, modulating our applications, going from monolithic applications to applications made up of modules, and to be able to share code more easily between our projects.