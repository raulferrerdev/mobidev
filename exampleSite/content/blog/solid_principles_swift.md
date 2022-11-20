---
title: "SOLID principles: Application to Swift development"
description: ""
image: "images/post/solid_principles_swift.png"
date: 2020-02-07T18:19:25+06:00
categories: ["Architecture","Development", "Swift"]
type: "Swift" # available types: [featured/regular]
draft: false
---
As a developer, you probably use or have heard about [SOLID principles](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod). This acronym, introduced by [Robert C. Martin](https://twitter.com/unclebobmartin) (Uncle Bob), in his book [Agile Software Development: Principles, Patterns, and Practices](https://www.goodreads.com/book/%20show%20/%2084985.Agile_Software_Development_Principles_Patterns_and_Practices), refers to five basic principles of object-oriented programming, which are related to Design Patterns.

If we apply these five principles:

* We will have flexible code, which we can easily change and that will be both reusable and maintainable.
* The software developed will be robust, stable and scalable (we can easily add new features).
* Together with the use of the Design Patterns, it will allow us to create software that is highly cohesive (that is, the elements of the system are closely related) and loosely coupled (the degree of dependence between elements is low).

### Origin of the acronym SOLID

The acronym SOLID comes from:

* **S** (SRP): Single responsibility principle
* **O** (OCP): Open/closed principle
* **L** (LSP): Liskov substitution principle
* **I** (ISP): Interface segregation principle
* **D** (DIP): Dependency inversion principle

#### Single responsibility principle

According to this principle, a class should have a reason, and only one, to change. That is, a class should only have one responsibility.

Let’s see an example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class LoginUser {
    func login() {
        let data = authenticareUserViaAPI()
        let user = decodeUser(data: data)
        saveToDB(array: array)
    }
    
    private func authenticareUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
    
    private func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
    
    private func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
{{< / highlight >}}

As noted, this class presents three responsibilities: *Authentification*, *Decode info* and *Save info*. To fulfill the principle of sole responsibility we extract each of these responsibilities in other smaller classes.
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class LoginUser {
    let oAuthHandler: OAuthHandler
    let decodeHandler: DecodeHandler
    let databaseHandler: DataBaseHandler
    
    init(oAuthHandler: OAuthHandler, decodeHandler: DecodeHandler, databaseHandler: DataBaseHandler) {
        self.oAuthHandler = oAuthHandler
        self.decodeHandler = decodeHandler
        self.databaseHandler = databaseHandler
    }
    
    func login() {
        let data = oAuthHandler.authenticareUserViaAPI()
        let user = decodeHandler.decodeUser(data: data)
        databaseHandler.saveUserInfoOnDatabase(user: user)
    }
}

class OAuthHandler {
    func authenticareUserViaAPI() -> Data {
        // Call server to authenticate and return user's info
    }
}

class DecodeHandler {
    func decodeUser(data: Data) -> User {
        // Decode data (Codable protocol) into User object
    }
}

class DataBaseHandler {
    func saveUserInfoOnDatabase(user: User) {
        // Save User info onto database
    }
}
{{< / highlight >}}

#### Open/closed principle

According to this principle, we must be able to extend the a class without changing its behaviour. This is achieved by abstraction.
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class Scrapper {

    func scrapVehicles() {
        let cars = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
        ]

        cars.forEach { car in
            print(car.getScrappingAddress())
        }

        let trucks = [
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        trucks.forEach { truck in
            print(truck.getScrappingAddress())
        }
    }
}

class Car {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrappingAddress() -> String {
        return "Trucks scrapping address"
    }
}
{{< / highlight >}}

For each new type of vehicle, the *getScrapingAddress()* function must be implemented again, which breaks the principle of open/closed. To solve this point, we introduce the *Scrappable* protocol that contains the *getScrappingAddress()* method:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
protocol Scrappable {
    func getScrapingAddress() -> String
}

class Scrapper {

    func getScrapingAddress() {
        let vehicles: [Scrappable] = [
            Car(brand: "Ford"),
            Car(brand: "Peugeot"),
            Car(brand: "Toyota"),
            Truck(brand: "Volvo"),
            Truck(brand: "Nissan"),
        ]

        vehicles.forEach { vehicle in
            print(vehicle.getScrapingAddress())
        }
    }
}

class Car: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Cars scrapping address"
    }
}

class Truck: Scrappable {
    let brand: String

    init(brand: String) {
        self.brand = brand
    }

    func getScrapingAddress() -> String {
        return "Trucks scrapping address"
    }
}
{{< / highlight >}}

#### Liskov substitution principle

This principle, introduced by [Barbara Liskov](https://en.wikipedia.org/wiki/Barbara_Liskov) in 1987, states that in a program any class should be able to be replaced by one of its subclasses without affecting its functioning.

For example, suppose we have a class, UserService, which has the responsibility of contacting users (for example, sending an email). If we change the business logic and, for example, we can only send mails to users over 17 years of age, we can create a subclass that adds the new business logic:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class UserService {
    func contact(user: User) {
        // Retrieve user from database
    }
}

class ValidUserService: Handler {
    override func contact(user: User) {
        guard user.age > 17 else { return }

        super.contact(user: User)
    }
}
{{< / highlight >}}


In this case, the Liskov substitution principle is not fulfilled, since the subclass adds a condition (that the age of the user is over 17 years old), which a customer of the *UserService* class would not expect. We can solve this problem by not creating the subclass, and adding the precondition to *UserService* (including a default value):
{{< highlight swift  "linenos=inline,linenostart=1" >}}

class UserService {
    func contact(user: User, minAge: Int = 0) {
        guard user.age > minAge else { return }
        // Retrieve user from database
    }
}
{{< / highlight >}}

#### Interface segregation principle

The Principle of segregation of the interface indicates that it is better to have different interfaces (protocols) that are specific to each client, than to have a general interface. In addition, it indicates that a client would not have to implement methods that he does not use.

For example, we can create an interface for animals that includes displacement methods:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

protocol AnimalProtocol {
    func walk()
    func swimm()
    func fly()
}

struct Animal: AnimalProtocol {
    func walk() {}
    func swimm() {}
    func fly() {}
}

struct Wale: AnimalProtocol {
    func swimm() {
        // Walw only needs to implement this function
        // All the other functions are irrelavant
    }
    
    func walk() {}
    
    func fly() {}
}
{{< / highlight >}}


However, although Wale adopts the protocol, there are two methods that he does not implement. The solution is to establish three interfaces (protocols), one per method:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

protocol WalkProtocol {
    func walk()
}

protocol SwimmProtocol {
    func swimm()
}

protocol FlyProtocol {
    func fly()
}

struct Wale: SwimmProtocol {
    func swimm() {}
}

struct Crocodile: WalkProtocol, SwimmProtocol {
    func walk()
    func swimm() {}
}
{{< / highlight >}}

#### Dependency inversion principle

According to the Dependency inversion principle:

* High level classes should not depend on low level classes. Both should depend on abstractions.
* Abstractions should not depend on the details. The details should depend on the abstractions.

This principle tries to reduce the dependencies between modules, and thus achieve a lower coupling between classes.

Let’s look at the following example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
class User {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class CoreData {
    func save(user: User) {
        // Save user on database
    }
}

class UserService {
    func save(user: User) {
        let database = CoreData()
        database.save(user: user)
    }
}
{{< / highlight >}}


What happens if instead of using CoreData to save the data, we want to use the Realm database? The instantiation of the class as we have done in the example generates a strong coupling, so if we want to use another database, we would have to redo the code.

To solve this, we can do what was explained in a previous article, when a database layer was established.
{{< highlight swift  "linenos=inline,linenostart=1" >}}

protocol Storable { }

extension Object: Storable { } // Realm Database

extension NSManagedObject: Storable { } // Core Data Database

protocol StorageManager {
  /// Save Object into Realm database
  /// - Parameter object: Realm object (as Storable)
  func save(object: Storable)
}
{{< / highlight >}}

Now we have *User* that adopts the *Storable* protocol and the *UserService* class adopts the *StorageManager* protocol, so even if we change the database, we will not need to change the entire implementation code:
{{< highlight swift  "linenos=inline,linenostart=1" >}}

class User: Storable {
    var name: String
    
    init(name: String) {
        self.name = name
    }
}

class UserService: StorageManager {
    func save(object: Storable) {
        // Saves user to database
    }
}
{{< / highlight >}}

### Conclusion

These principles will allow us to reduce the rigidity and fragility of the code, by allowing us to create reusable, maintainable and higher quality components in your code.