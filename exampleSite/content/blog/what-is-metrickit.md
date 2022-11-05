---
title: "What is MetricKit?"
description: ""
image: "images/post/what_is_metrickit.jpg"
date: 2019-10-26T18:19:25+06:00
categories: ["Swift","Development"]
type: "featured" # available types: [featured/regular]
draft: false
---

**MetricKit** in a new framework, introduced in iOS 13 (WWDC2019), with which it is intended to gather information (metrics) about battery behavior and application performance. For a registered application, reports on the behavior of said application will be received once a day as a general rule.
### Battery

Battery metrics include, among other parameters:

* The state of the mobile network.
* The transfers through the internet.
* The use of the CPU.
* The use of the GPU.
* The energy used to display the application on the screen.
* The use of device location functions.


### Performance

From the point of view of performance, metrics are obtained, for example, on:

* The time it takes for the application to open (launch time).
* The response of the application with user interaction.
* The amount of time the application is active.
* The use of memory and disk.

### Use

In order to receive metric reports, we need to add a subscription to *MXMetricManager* (for example in the *AppDelegate* class):
{{< highlight swift >}}
class AppDelegate: UIResponder, UIApplicationDelegate, MXMetricManagerSubscriber {

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        let shared = MXMetricManager.shared
        shared.add(self)

        return true
    }

    func applicationWillTerminate(_ application: UIApplication) {
        MXMetricManager.shared.remove(self)
    }

    // Receive daily metrics
    func didReceive(_ payloads: [MXMetricPayload]) {
       // Process metrics
    }
}
{{< / highlight >}}
The *didReceive* method receives an array that contains the information of the last 24 hours (and of all that information that could not be received previously).
