---
title: "What is new in Swift 5.3"
description: "In this post he explains some of the most important new features that Swift 5.3 introduces."
image: "images/post/new_swift_5_3.png"
thumbnail: "images/post/new_swift_5_3.png"
date: 2020-01-18T18:19:25+06:00
categories: ["Swift basics"]
tags: ["Swift"]
draft: false
---
What is new in **Swift 5.3** (whose publication process was announced by [Apple on 3/25](https://swift.org/blog/5-3-release-process/)) can be found both at the code level (multi-pattern catch, multiple trailing closure …) and in the use of Swift Package Manager (addition of binary dependencies or resources. Below I detail some of the new features in Swift 5.3 (keep in mind that we expect to see the new beta of Swift 5.3 at WWDC20, although to test it you can download it at [Swift.org](https://swift.org/download/#snapshots)).
#### Synthesized Comparable conformance for enums ([SE-0266](https://github.com/apple/swift-evolution/blob/master/proposals/0266-synthesized-comparable-for-enumerations.md))

Thanks to this new addition, we can adopt the **Comparable** protocol in enums that do not have associated values, or that do have them and they adopt the Comparable protocol. With this, what is achieved is to be able to compare two cases of the same enum using, for example, > or <.
{{< highlight swift  "linenos=inline,linenostart=1" >}}

enum OperationOrder: Comparable {
    case addition
    case multiplication
    case exponent
}

let operation1 = OperationOrder.addition
let operation2 = OperationOrder.exponent

if operation1 < operation 2 {
    print("Do operation 2 first.")
} else {
    print("Do operation 1 first.")
}
{{< / highlight >}}

#### Use where on contextually generic declarations ([SE-0267](https://github.com/apple/swift-evolution/blob/master/proposals/0267-where-on-contextually-generic.md))

What is looking for with this modification is to remove the restriction of attaching where clauses to member declarations that can only refer to external generic parameters. For example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

struct Operation<T> { }  

extension Operation where T.Element: Equatable {
    func doOperation() { // Code }
}
extension Operation where T.Element: Hashable {
    func recalculate() { // Code }
}
extension Foo where T.Element == Int {
    func resetOperation() { // Code }
}
{{< / highlight >}}


With Swift 5.3 it can be expressed as:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

extension Foo where T.Element: Equatable {
    func doOperation() { // Code }

    func recalculate() where T.Element: Hashable { // Code }

    func resetOperation() where T.Element == Int { // Code }
}
{{< / highlight >}}

#### Redefinition of didSet semantics ([SE-0268](https://github.com/apple/swift-evolution/blob/master/proposals/0268-didset-semantics.md))

Prior to Swift 5.3, when using didSet in a property, the getter was always called to obtain the oldValue of that property (although this value was not referenced), with the consequent performance impact. In Swift 5.3 the efficiency of the process has been increased, since now the oldValue is not accessed if we are not calling it inside didSet.
#### Self use in closures is eliminated if no reference cycles are given ([SE-0269](https://github.com/apple/swift-evolution/blob/master/proposals/0269-implicit-self-explicit-capture.md))

Until now, inside closures it was necessary to use self explicitly, even if reference cycles were not going to occur. For example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

class Calculator {
    var result = 0

    func calculate(operation: @escaping () -> void) {
        operation()
    }

    func calculation() {
        calculate { [self] in 
          self.result = 1
        }
    }
}
{{< / highlight >}}


With Swift 5.3 self can be removed:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

class Calculator {
    var result = 0

    func calculate(operation: @escaping () -> void) {
        operation()
    }

    func calculation() {
        calculate {
            result = 1
        }
    }
}
{{< / highlight >}}

#### Multi-Pattern Catch Clauses ([SE-0276](https://github.com/apple/swift-evolution/blob/master/proposals/0276-multi-pattern-catch-clauses.md))

Until now, Swift only allowed the use of one error type per catch block, allowing us to reduce duplicate code in error handling. That is to say:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

enum CalculateError: Error {
    case infinite
    case overflow
}

func doOperation() {
    do {
        try calculation()
    } catch CalculateError.infinite {
        print("Infinite result")
    } catch CalculateError.overflow {
        print("Overflow error")
    }
}
{{< / highlight >}}


Now with Swift 5.3 we can merge both errors into a single catch block:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func doOperation() {
    do {
        try calculation()
    } catch CalculateError.infinite, CalculateError.overflow {
        print("Can't do operation")
    }
}
{{< / highlight >}}

#### A new Float16 type ([SE-0277](https://github.com/apple/swift-evolution/blob/master/proposals/0277-float16.md))

In Swift 5.3 this new type is added due to its widespread use in mobile graphics, HDR images and Machine Learning.
Multiple-trailing closures (SE-0279)

A trailing closure refers to the fact that the last parameter of a function in Swift is a closure. In this case, Swift allows you to pass this closure directly inside the brackets instead of as a parameter:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func calculate(operation: () -> Void) {
    print("Initial steps")
    operation()
    print("Show result")
}

calculate() {
    print("Sum 1 + 1")
}
{{< / highlight >}}


With Swift 5.3 this option is not limited to the last parameter of the function, so now we have to add multiple closures to our functions (simply by adding new labels):
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func calculate(values: [Int], operation: () -> Void, showResult: () -> Void) {
    operation()
}

func performOperation() {
    calculate(values: [1, 2]) { (operation)
        print("Perform operation")
    }, showResult: { (result) in
        print(result)
    }
}
{{< / highlight >}}

#### Swift Package Manager improvements

For those of us who use [Swift Package Manager](https://www.raulferrergarcia.com/en/how-to-use-swift-package-manager/) to create and import Swift packages, Swift 5.3 presents some very interesting improvements:

* **Package Manager Resources ([SE-0271](https://github.com/apple/swift-evolution/blob/master/proposals/0271-package-manager-resources.md))**. Now Swift packages can incorporate resources (images, audio …). But not only that, but certain rules may be applied to process these resources.
* **Package Manager Binary Dependencies ([SE-0272](https://github.com/apple/swift-evolution/blob/master/proposals/0272-swiftpm-binary-dependencies.md))**. You can use dependencies in binary format, such as Firebase or GoogleAnalytics.
* **Conditional dependencies ([SE-0273](https://github.com/apple/swift-evolution/blob/master/proposals/0273-swiftpm-conditional-target-dependencies.md))**. This option will allow specifying which dependencies are added depending on the platform.
* **Localized resources ([SE-0278](https://github.com/apple/swift-evolution/blob/master/proposals/0278-package-manager-localized-resources.md))**. Along with the possibility of adding resources to Swift packages, they can be localized.

#### Conclusion

Swift 5.3 includes interesting improvements at the code or Swift package management level. But there are other improvements to look out for as well, such as the fact that Swift 5.3 will support platforms like Windows and new [Linux](https://swift.org/blog/additional-linux-distros/) distributions.