---
title: "Custom Codable types"
description: "Codabe, Decodable and Encodable are protocols that will allow us to work easily with information in JSON format within our applications. Use Custom Keys to be able to modify the name of the JSON parameters."
image: "images/twittercard.png"
thumbnail: "images/twittercard.png"
date: 2017-12-09T18:19:25+06:00
categories: ["Swift basics"]
tags: ["Swift"]
draft: false
---
We are going to see how we can encode and decode information in JSON format with our own keys, different from those that arrive in the JSON from a server. For this we will use custom Codable types.

In a previous publication we saw how to transform data in JSON format, which we had obtained from an external source (such as when making a request to a web service), into structures (struct) or classes (class) using the Codable protocol.

In that publication, the properties of the structures or classes had the same name as the keys (keys) in the JSON. That is, if the key in the JSON was firstName, then the property of the structure associated with that value is also firstName:
{{< highlight swift "linenos=inline,linenostart=1" >}}
{
  "id": "39631383-4e2a-48ef-bb40-f9d896392eab",
  "firstName": "Jane",
  "lastName": "Doe",
  "mail": "jane.doe@example.com",
  "active": "true"
}

struct User: Codable {
  let id: String
  let firstName: String
  let lastName: String
  let mail: String
  let active: String
}
{{< / highlight >}}

#### Custom keys

However, what happens when the JSON keys do not match the name of the properties of our structures or classes (for example, due to naming conventions). For example, suppose that in the User data structure we use the firstName parameter, but from the server we get a JSON with the first_name key.

In that case, we can assign our own values ​​using custom Codable types. For example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
{
  "user_id": "39631383-4e2a-48ef-bb40-f9d896392eab",
  "first_name": "Jane",
  "last_name": "Doe",
  "mail": "jane.doe@example.com",
  "active": "true"
}
{{< / highlight >}}

Now we extend our struct or class by defining an Enum CodingKeys. In this extension we will assign the values ​​of the custom Codable types to the cases that we want to match:

{{< highlight swift  "linenos=inline,linenostart=1" >}}
extension User {
  enum CodingKeys: String, CodingKey {
    case id = "user_id"
    case firstName = "first_name"
    case lastName = "last_name"
    case mail
    case active
  }
}
{{< / highlight >}}

#### Encoding example

To move from an instance of a structure to a JSON (if we use the same type of User structure and the same JSON):

{{< highlight swift  "linenos=inline,linenostart=1" >}}
var user = User()
user.id = "39631383-4e2a-48ef-bb40-f9d896392eab"
user.firstName = "Jane"
user.lastName = "Doe"
user.mail = "jane.doe@example.com"
user.active = "true"

do {
    let jsonData = try JSONEncoder().encode(user)
    print(String(decoding: jsonData, as: UTF8.self))
} catch {
    print(error)
}
{{< / highlight >}}

In this way we get the following in the console:

{{< highlight swift >}}
{"user_id":"39631383-4e2a-48ef-bb40-f9d896392eab","first_name":"Jane","last_name":"Doe","mail":"jane.doe@example.com","active":"true"}
{{< / highlight >}}

#### Decoding example

For the reverse step, the decoding of JSON to struct, we have:

{{< highlight swift  "linenos=inline,linenostart=1" >}}
let userJson = """
{
  "user_id": "39631383-4e2a-48ef-bb40-f9d896392eab",
  "first_name": "Jane",
  "last_name": "Doe",
  "mail": "jane.doe@example.com",
  "active": "true"
}
"""

let jsonData = userJson.data(using: .utf8)!
do {
    let user = try JSONDecoder().decode(User.self, from: jsonData)
    print(user)
} catch {
    print(error.localizedDescription)
}
{{< / highlight >}}

And in the console we obtain:

{{< highlight swift >}}
"User(id: "39631383-4e2a-48ef-bb40-f9d896392eab", firstName: "Jane", lastName: "Doe", mail: "jane.doe@example.com", active: "true")"
{{< / highlight >}}
