---
title: "SceneDelegate on iOS 13"
description: "iOS 13 hga introduced SceneDelegate and the new concept of scene. Here's what this means and how it affects AppleDelegate."
image: "images/twittercard.png"
thumbnail: "images/twittercard.png"
date: 2020-03-07T18:19:25+06:00
categories: ["Swift basics"]
tags: ["Swift"]
draft: false
---
When we run an application on iOS, the first function called is *(_: didFinishLaunchingWithOptions:)* in the AppDelegate. In the AppDelegate we can also find a series of functions that are executed when the state of the application changes (going to the background, returning to the foreground, etc.). With Xcode 11, a new SceneDelegate class appears when generating a new project. But, what is the function of SceneDelegate in iOS 13. Now, in the **SceneDelegate** class in iOS 13 the concept of window (window) has been changed to [scene](https://developer.apple.com/documentation/uikit/app_and_environment/scenes), since now an application can have more than one scene . That is, we can open two or more windows of the same application and interact with each other.
#### Functions of the SceneDelegate class

The main function in the SceneDelegate class is:
{{< highlight swift  "linenos=inline" >}}
 func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions)
{{< / highlight >}}

By default, this function creates the initial view, a new window (*UIWindow*), sets its controller (*rootViewController*) and makes it the main window (*key window*). In this case, the *UISceneSession* instance that we use in *UIWindow(windowScene:)* controls the window that the user sees.

This function, *scene(_: willConnectTo: options:)*, also allows you to restore a scene if it has previously been deactivated (as, for example, when going to the background).

In the *SceneDelegate* class we find some of the functions that were in the *AppDelegate* class, but referred to the scenes:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
func sceneDidDisconnect (_ scene: UIScene) // It is called every time a 'scene' is deleted by the system
 
func sceneDidBecomeActive (_ scene: UIScene) // Called when a 'scene' goes from inactive to active state
 
func sceneWillResignActive (_ scene: UIScene) // It is called when a 'scene' goes from active to inactive state
 
func sceneWillEnterForeground (_ scene: UIScene) // It is called when a 'scene' goes from the background to the foreground
 
func sceneDidEnterBackground (_ scene: UIScene) // It is called when a 'scene' goes from foreground to background (background)
{{< / highlight >}}

Once a scene is created or reconnected, the first method that is called is *sceneWillEnterForeground(_ scene:)* when the scene will appear (either because it is activated for the first time or because it goes from second to foreground).

Next, the *sceneDidBecomeActive(_ scene:)* method is called, when the scene is ready to receive and respond to user events.When the application is about to stop responding to user events, the *sceneWillResignActive(_ scene:)* method is called, and when it goes from the foreground to the background, the *sceneDidEnterBackground(_ scene:)* method.
#### New methods on the AppDelegate class

On the other hand, in the *AppDelegate* class there are two new methods related to the scenes, since now the new life cycle of the scenes sessions is managed by *UIApplicationDelegate*:
{{< highlight swift  "linenos=inline,linenostart=1" >}}
func application (_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration // It is called when a new 'scene session' is created
 
func application (_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set <UISceneSession>) // Called when the user discards a 'scene session'
{{< / highlight >}}

#### Modification in the Info.plist file

Together with these new delegates, Xcode creates a new entry (*Application Scene Manifest*) in the *Info.plist* file:
{{< image src="images/post/scenedelegate_ios_13_1.png" >}}
This new entry we find these fields:

* **Enable Multiple Windows**. If its value is *TRUE*, users can open multiple windows of an application (on *iPadOS*).
* **Scene Configuration**. It contains the *Application Session Role* field, an array (so we could have multiple configurations), whose first item contains a name and a delegate for the scene.