---
title: "Detect Net status with NWPathMonitor"
description: ""
image: "images/post/detect_net_satus_with_nwpathmonitor.jpg"
date: 2018-11-11T18:19:25+06:00
categories: ["Swift","Development", "Connectivity", "Net"]
type: "regular" # available types: [featured/regular]
draft: false
---
With iOS 12, Apple has introduced Network, a framework that includes the NWPathMonitor class. NWPathMonitor gives us the means to monitor changes of state in the internet connection (so it is no longer necessary to use the Reachability class, in applications that support iOS 12 onwards). Therefore, we can set aside the Reachability library, and detect the state of the network with NWPathMonitor.

To use this new way to check the status of the internet connection, we first need to create an instance of NWPathMonitor:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
 let monitor = NWPathMonitor()
{{< / highlight >}}


We can also instantiate the NWPathMonitor class indicating a particular type of network that we want to check. For example, to check WiFi connections:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
 let monitor = NWPathMonitor(requiredInterfaceType: .wifi)
{{< / highlight >}}

NWPathMonitor can check different types of interfaces:

* **cellular.** For 3G / 4G connections.
* **loopback.** For localhost.
* **other.** For virtual networks or unknown network types.
* **wifi.** For WiFi connections.
* **wiredEthernet.** If the device is connected to the internet by cable.

Detection of changes in the state of the internet connection are made through the pathUpdateHandler property:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
monitor.pathUpdateHandler = { path in
    if path.status == .satisfied {
        print("!Hay conexión con internet!")
    }
}
{{< / highlight >}}

path is of NWPath type and, according to Apple documentation, status can be:

* **unsatisfied.** The connection (path) cannot be used.
* **satisfied.** The connection (path) has been established and allows data to be sent.
* **requiresConnection.** The connection (path) is not currently available, but if a new connection is established it can be activated.

For more information on the possibilities of NWPath we can access the Apple documentation.
Finally, in order to start receiving information about changes in the state of the internet connection, we need to call the start () method. The start () method needs a queue to do this job:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
let queue = dispatchQueue.global(qos: .background)
monitor.start(queue: queue)
{{< / highlight >}}


Once we no longer need to know the changes in the state of the internet connection, we will call the cancel () method.
#### Code example

For example, suppose we want to know at all times the status of the internet connection throughout the application. For this we can use a class of type Singleton:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
import Foundation
import Network

class NetMonitor {
    static public let shared = NetMonitor()
    private var monitor: NWPathMonitor
    private var queue = DispatchQueue.global()
    var netOn: Bool = true
    var connType: ConnectionType = .wifi

    private init() {
        self.monitor = NWPathMonitor()
        self.queue = DispatchQueue.global(qos: .background)
        self.monitor.start(queue: queue)
    }

    func startMonitoring() {
        self.monitor.pathUpdateHandler = { path in
            self.netOn = path.status == .satisfied
            self.connType = self.checkConnectionTypeForPath(path)
        }
    }

    func stopMonitoring() {
        self.monitor.cancel()
    }

    func checkConnectionTypeForPath(_ path: NWPath) -> ConnectionType {
        if path.usesInterfaceType(.wifi) {
            return .wifi
        } else if path.usesInterfaceType(.wiredEthernet) {
            return .ethernet
        } else if path.usesInterfaceType(.cellular) {
            return .cellular
        }

        return .unknown
    }
}
{{< / highlight >}}


Where ConnectionType is an enum that contains the specific cases of connectivity for our app. For example:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
public enum ConnectionType {
    case wifi
    case ethernet
    case cellular
    case unknown
}
{{< / highlight >}}


To use this Singleton type class:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
let netConnection = NetMonitor.shared
netConnection.start() // Internet connection monitoring starts
netConnection.cancel() // Internet connection monitoring stops
let status = netConnection.connType // Returns the connection type
{{< / highlight >}}
