---
title: "Improve your Swift code with higher order functions"
description: ""
image: "images/post/swift_high_order_functions.png"
date: 2020-06-09T18:19:25+06:00
categories: ["Swift"]
tags: "Swift"
draft: false
---
In previous articles we had seen how to improve the architecture of our code through [design patterns](https://raulferrergarcia.netlify.app/blog/design_patterns_software/) and the use of [SOLID principles](https://raulferrergarcia.netlify.app/blog/solid_principles_swift/). Now we will see how to improve the Swift code with higher order functions. Surely you have used them more than once, but what are they and how do you improve your Swift code with higher order functions?
#### Swift higher order functions

Higher-order functions are functions that take other functions or closures as arguments and that return a function or a closure. These functions are used with arrays, sets and dictionaries, and act on the elements they contain (this is done by methods that are applied to the elements of the collection using the point syntax).

Some of the best-known functions are **map**, **compactMap**, **flatMap**, **reduce**, **filter**, **contains**, **sorted**, **forEach** or **removeAll**.
##### map

The map function works by performing an operation on all the elements of a collection and returning a new collection with the results of that operation.

For example, let’s suppose that we have an array with several words with all their letters in lowercase and we want to obtain a new array with each of these words but with all the uppercase letters. We could do this with a *for…in* loop:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]
var uppercasedWords: [String] = [String]()

for word in words {
    uppercasedWords.append(word.uppercased())
}

// uppercasedWords = ["ROOM", "HOME", "TRAIN", "GREEN", "HEROE"]
{{< / highlight >}}

Let’s see how to do it with the map function. As shown in the [Apple documentation](https://developer.apple.com/documentation/swift/array/3017522-map), map is declared as:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func map<T>(_ transform: (Element) throws -> T) rethrows -> [T]
{{< / highlight >}}

Where transform accepts an element from a collection or sequence as a parameter and returns a transformed value of the same type or a different one.

In the example we are seeing we apply it as follows:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

let uppercasedWords = words.map({ word in
    return word.uppercased()
})
{{< / highlight >}}


What **map** does is loop through the entire array of elements, apply the uppercased() method on each of them, and return a new array with these values.

Anyway, we can reduce this expression by using the shorthand argument **$0**, which refers to any elements of the array:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let uppercasedWords = words.map({ $0.uppercased() })
{{< / highlight >}}

##### compactMap

Now suppose that inside the array of the previous example there are nil values. If we use the **map** function, we must take into account whether the value to be acted on is nil or not:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String?] = ["room", "home", nil, nil, "train", nil, nil, "green", "heroe", nil]

let uppercasedWords = words.map({ word -> String? in
    if let word = word {
        return word.uppercased()
    } else {
        return nil
    }
})

// Or using the shorthand notation:
let uppercasedWords = words.map { $0 != nil ? $0!.uppercased() : nil }

// uppercasedWords = ["ROOM", "HOME", nil, nil, "TRAIN", nil, nil, "GREEN", "HEROE", nil]
{{< / highlight >}}

But what if what we really want is to get the new array but without the nil values? To achieve this we have the **compactMap** function.

The [compactMap](https://developer.apple.com/documentation/swift/array/2957701-compactmap) function returns an array containing the non-nil results after applying the given transformation to each element of a sequence.
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func compactMap<ElementOfResult>(_ transform: (Element) throws -> ElementOfResult?) rethrows -> [ElementOfResult]
{{< / highlight >}}

Therefore, in the example we are seeing:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let uppercasedWords = words.compactMap {&nbsp;$0.uppercased() }

// uppercasedWords = ["ROOM", "HOME", "TRAIN", "GREEN", "HEROE"]
{{< / highlight >}}

In other words, **compactMap** loops through all the elements of the array and applies the method to non-nil values, returning them in an array, in this case of type [String] (that is, the value of String is not optional).
##### flatMap

The **flatMap** function allows us to transform a set of arrays into a single set that contains all the elements. As Apple declares in its [documentation](https://developer.apple.com/documentation/swift/sequence/2905332-flatmap):
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func flatMap<SegmentOfResult>(_ transform: (Self.Element) throws -> SegmentOfResult) rethrows -> [SegmentOfResult.Element] where SegmentOfResult : Sequence
{{< / highlight >}}


For example, let’s first look at how we would do it without **flatMap**:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [[String]] = [["room", "home"], ["train", "green"], ["heroe"]]
var singleArray: [String] = [String]()

for individualArray in words {
    for word in individualArray {
        singleArray.append(word)
    }
}

// singleArray = ["room", "home", "train", "green", "heroe"]
{{< / highlight >}}


But with **flatMap** we can simplify the code as follows:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [[String]] = [["room", "home"], ["train", "green"], ["heroe"]]

let singleArray = words.flatMap { $0 }

// singleArray = ["room", "home", "train", "green", "heroe"]
{{< / highlight >}}

##### reduce

[reduce](https://developer.apple.com/documentation/swift/array/2298686-reduce) is a function that, when applied to a collection, returns the result of combining the elements of that collection:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func reduce<Result>(_ initialResult: Result, _ nextPartialResult: (Result, Element) throws -> Result) rethrows -> Result
{{< / highlight >}}


For example, if we have an array with the numbers from 1 to 10 and we want to obtain their sum. We can do it in the following way without using the reduce function:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let numbers: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

var result: Int = 0

for number in numbers {
    result += number
}

// result = 55
{{< / highlight >}}


With **reduce**, we simply do the following:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let numbers: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

let sum = numbers.reduce(0, { x, y in
    return x + y
})

// sum = 55
{{< / highlight >}}


In the first iteration the value of x is 0 (as we have indicated in the function) and the value of y is 1. So the result x + y will be 1. In the second iteration x is 1 and y is 2, so the result will be 3. And so on.

In a more simplified way we can write:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let sum = numbers.reduce(0, { $0 + $1 })
{{< / highlight >}}

##### filter

As its name suggests, the [filter](https://developer.apple.com/documentation/swift/sequence/3018365-filter) function filters the content of a collection and returns a new collection that contains the elements that meet a certain condition:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func filter(_ isIncluded: (Self.Element) throws -> Bool) rethrows -> [Self.Element]
{{< / highlight >}}


For example, suppose we have the word collection from the first examples and we want to get a new collection of words containing the letter ‘o’. Without the filter function, we could do it in the following way:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

var wordsWithO: [String] = [String]()

for word in words {
    for letter in word {
        if letter == "o" {
            wordsWithO.append(word)
            break
        }
    }
}
{{< / highlight >}}


In this case, we have not used the **contains** function since, as we will see later, it is also a higher order function.

Now we are going to simplify this code using the **filter** function:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

let wordsWithO = words.filter { $0.contains("o") }

// wordsWithO = ["room", "home", "heroe"]
{{< / highlight >}}


But it does not have to apply a single condition, we can apply several conditions. For example, in the previous case we can make it return the words that contain the vowel ‘o’ and whose length is 5 characters or more:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

let worthWithO = words.filter { $0.contains("o") && $0.count >= 5 }

// worthWithO = ["heroe"]
{{< / highlight >}}

##### contains

In the previous example we have used the contains function to determine if a word contained the vowel ‘o’. Well, **contains** is a higher-order function that allows you to check if there are elements that meet a certain condition and return true or false depending on whether or not they meet it.

As Apple indicates in its [documentation](https://developer.apple.com/documentation/swift/array/2945493-contains), contains returns a boolean value that indicates whether the sequence contains the given element.
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func contains(_ element: Element) -> Bool
{{< / highlight >}}

##### sorted

On numerous occasions we find a collection of elements that we want to order in some way to show them. For example, in the word array examples seen so far, those words are not arranged alphabetically.

But what if we wanted to order them alphabetically? We could use some algorithm, like:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

var words: [String] = ["room", "home", "train", "green", "heroe"]

var swapWord: Bool = false

repeat {
    swapWord = false
    
    for n in 0...words.count - 2 {
        if words[n] &gt; words[n + 1] {
            let temporalWord = words[n + 1]
            words[n + 1] = words[n]
            words[n] = temporalWord
            swapWord = true
        }
    }

    
} while swapWord

// words = ["green", "heroe", "home", "room", "train"]
{{< / highlight >}}

We could reduce this code using the [swapAt](https://developer.apple.com/documentation/swift/array/2893281-swapat) method, which allows us to exchange the positions of two elements in a sequence:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

var words: [String] = ["room", "home", "train", "green", "heroe"]

var swapWord: Bool = false

repeat {
    swapWord = false
    
    for n in 0...words.count - 2 {
        if words[n] > words[n + 1] {
            words.swapAt(n, n + 1)
            swapWord = true
        }
    }

    
} while swapWord

// words = ["green", "heroe", "home", "room", "train"]
{{< / highlight >}}

To further reduce this code, we can use the [sorted](https://developer.apple.com/documentation/swift/array/2945003-sorted) function. This function returns the elements of a sequence ordered in ascending order (as long as the elements of the collection adopt the Comparable protocol):
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func sorted() -> [Element]
{{< / highlight >}}

{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

let sortedWords = words.sorted()

// sortedWords = ["green", "heroe", "home", "room", "train"]
{{< / highlight >}}

On the other hand, if we want to use our own condition to order the collection, we use the function **sorted(by:)**, which as Apple indicates in its [documentation](https://developer.apple.com/documentation/swift/array/2296815-sorted), returns the elements of the sequence, ordered using the predicate given as the comparison between elements.
{{< highlight swift  "linenos=inline,linenostart=1" >}}

func sorted(by areInIncreasingOrder: (Element, Element) throws -> Bool) rethrows -> [Element]
{{< / highlight >}}


For example, if we want the words to be ordered in reverse alphabetical order, we can do the following:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

let sortedWords = words.sorted(by: > )

//sortedWords = ["train", "room", "home", "heroe", "green"]
{{< / highlight >}}


In this case, by indicating the symbol ‘>’ we order the collection in descending order.
##### forEach

It performs a function similar to **for…in**, but unlike this, neither continue nor break can be used inside [forEach](https://developer.apple.com/documentation/swift/array/1689783-foreach), just return:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

let words: [String] = ["room", "home", "train", "green", "heroe"]

words.forEach({ word in
    guard word.count &gt; 4 else {
        print(word.uppercased())
        return
    }

    print(word)
})

// ROOM
// HOME
// train
// green
// heroe
{{< / highlight >}}

##### removeAll

The higher order function [removeAll(where:)](https://developer.apple.com/documentation/swift/array/3017530-removeall) allows us to remove elements from a sequence that meet certain conditions:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

mutating func removeAll(where shouldBeRemoved: (Element) throws -> Bool) rethrows
{{< / highlight >}}


For example, if we want to remove all the even numbers from a sequence, we can do the following using **removeAll**:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

var numbers: [Int] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

numbers.removeAll(where: { $0 % 2 == 0 })

// numbers = [2, 4, 6, 8, 10]
{{< / highlight >}}

The power of **removeAll(where:)** is seen more clearly in the example that Apple shows in its documentation, where it uses it to remove the vowels of a phrase:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

var phrase = "The rain in Spain stays mainly in the plain."

let vowels: Set<Character> = ["a", "e", "i", "o", "u"]

phrase.removeAll(where: { vowels.contains($0) })

// phrase == "Th rn n Spn stys mnly n th pln."
{{< / highlight >}}

#### Function concatenation

The first-order functions can be applied consecutively, concatenated. For example, we can take an array containing arrays of numbers and calculate their sum:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
let numbers: [[Int]] = [[1, 3, 6, 2], [2, 5, 7], [1, 3]]

let sum: Int = numbers
    .flatMap({ $0 })
    .reduce(0, {$0 + $1})

// sum = 30
{{< / highlight >}}

First we apply the **flatMap** function to obtain an array with all the numbers. Then we apply the **reduce** function to add them.
#### Conclusion

We have just seen some of the most commonly used higher order functions and their power using some examples. These functions allow, on the one hand, to reduce the amount of code and, on the other, to make it clearer and more concise.