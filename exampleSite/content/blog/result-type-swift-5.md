---
title: "Result type in Swift 5"
description: ""
image: "images/post/result_type_in_swift_5.png"
date: 2018-04-06T18:19:25+06:00
categories: ["Swift","Development"]
type: "regular" # available types: [featured/regular]
draft: false
---

In [Swift 5](https://swift.org/blog/swift-5-released/) several new features have been introduced. One of them is the **Result** type. The **Result** type in swift 5 is an enum that contains two cases: *success* and *failure*.
{{< highlight swift  "linenos=table,linenostart=1" >}}
public enum Result<Success, Failure: Error> {
    case success(Success), failure(Failure)
}
{{< / highlight >}}

**Result** permite manejar errores para API asíncronas de una forma automática. Por ejemplo, para realizar una llamada a un servidor que devuelve una lista de usuarios (struct User), podemos implementar el siguiente método:
{{< highlight swift  "linenos=table,linenostart=1" >}}
func fetchUsers(url: URL, completion: @escaping (Result<[User], Error>) -> Void) {
    URLSession.shared.dataTask(with: url) { data, response, error in
        guard let data = data, error == nil else {
            if let error = error as Error? {
                completion(.failure(error))
            }
            return
        }

        do {
            let users = try JSONDecoder().decode([User].self, from: data)
            completion(.success(users))
        } catch let error as? Error {
            completion(.failure(error))
        }

    }.resume()
}
{{< / highlight >}}

Now, we can call the *fetchUsers* method as follows:
{{< highlight swift  "linenos=table,linenostart=1" >}}
let  url = 'FETCH_USERS_URL'

fetchUsers(url: url) { result in
    switch result {
        case .success(let users):
            // Code to process user information.
        case .failure(error):
            // Code to handle error cases.
    }
}
{{< / highlight >}}
