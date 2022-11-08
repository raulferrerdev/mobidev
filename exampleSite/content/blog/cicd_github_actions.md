---
title: "Continuous Integration and Continuous Delivery (CI/CD) with GitHub Actions"
description: ""
image: "images/post/cicd_github_actions.png"
date: 2020-01-21T22:19:25+06:00
categories: ["Swift","Development"]
type: "regular" # available types: [featured/regular]
draft: false
---
What is Continuous Integration and Continuous Delivery of software? What does CI/CD mean? When different developers work together in an application, if the merge of code occurs at the same time, numerous problems can occur: conflicts between different developers’ code, errors … which makes this process slow. This is where Integration and continuous distribution comes in.
### Continuous Integration (CI)

Continuous integration (CI) allows different developers to upload and merge code changes in the same repository branch on a frequent basis. Once the code has been uploaded, it is validated automatically by means of unit and integration tests (both the uploaded code and the rest of the components of the application). In case of an error, this can be corrected more simple.
### Continuous Delivery (CD)

In the Continuous Delivery (CD) the new code introduced and that has passed the Continuous Integration process is automatically published in a production environment (this implementation may require manual approval). What is intended is that the repository code is always in a state that allows its implementation in a production environment.
### Continuous Deployment (CD)

In Continuous Deployment (CD), all changes in the code that have gone through the previous two stages are automatically implemented in production.
### Tools to perform CI/CD

There are numerous tools to configure and perform the entire CI/CD process:

* [Jenkins](https://jenkins.io/). Free.
* [TravisCI](https://travis-ci.org/). Free for open-source repository.
* [Nevercode](https://nevercode.io/). Commercial.
* [CircleCI](https://circleci.com/). Commercial (there is a limited free version).
* [Bitrise](https://www.bitrise.io/). Commercial (there is a limited free version).
* [Visual Studio App Center](https://appcenter.ms/). Commercial (there is a limited free version).
{{< image src="images/post/cicd_github_actions_1.png">}}

### GitHub Actions

[GitHub Actions](https://github.com/features/actions) is a new tool to perform the CI/CD process, [introduced in October 2018](https://github.blog/2018-10-17-action-demos/), launched in beta in August 2019, and finally distributed in November 2019. GitHub Actions is also paid, although it has a free version with some limitations.

{{< image src="images/post/cicd_github_actions_2.png">}}

#### Example of using Github Actions in an iOS/Swift project

To show how to use Github Actions in the CI / CD process on iOS, I will use [my own project](https://github.com/raulferrerdev/GHFollowers) (based on a [Sean Allen](https://seanallen.co/) course).

First, we enter our GitHub project and access the Actions tab:
{{< image src="images/post/cicd_github_actions_3.png">}}

We access a screen that allows us to choose between a predefined workflow or establish our own. In our case we will choose to establish our own.
{{< image src="images/post/cicd_github_actions_4.png">}}

We see how there is a workflow in a file called main.yml, which is in .github/workflows/. We can have different files with different workflows.
{{< image src="images/post/cicd_github_actions_5.png">}}

Now we will change the content of the file so that on the one hand it executes the application and, on the other, with the programmed tests.

{{< highlight swift >}}
 <script src="https://gist.github.com/raulferrerdev/e07a8341d8ee7c223c25074dcd3afaf4.js"></script>
{{< / highlight >}}

##### name
It is a label for our workflow.
##### on
Github executes workflows that are defined with the on key. In our case any push event. We can also define a specific branch. For example, push events in the devel branch:
{{< highlight swift >}}
 on:
   push:
     branches:
     - devel
{{< / highlight >}}

##### jobs

Each workflow is made up of one or more jobs. In our case there is only one.
##### runs-on

This parameter contains the type of virtual machine in which the code will be executed. In this case we will use macos-latest (to see the different types of virtual machines available you can access here).
##### strategy and matrix

A strategy creates a matrix of environments in which to execute the work. Each matrix allows to establish a set of different configurations of the virtual environment. In our case, we set the iOS simulator, with the operating system 13.2.2 and an iPhone 11 Pro Max device:
{{< highlight swift >}}
 strategy:
         matrix:
           destination: ['platform=iOS Simulator,OS=13.2.2,name=iPhone 11 Pro Max']
{{< / highlight >}}

##### steps

A step is a sequence of tasks. In the example we are seeing, the first step is to check the repository so that the workflow can access it. In this case to the master branch.
{{< highlight swift >}}
 - name: Checkout
   uses: actions/checkout@master
{{< / highlight >}}

In the second step the application is built in the environment defined above (destination):
{{< highlight swift >}}
 - name: Build
     run: |
        xcodebuild clean build -project GHFollowers.xcodeproj -scheme GHFollowers -destination "${destination}" CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO ONLY_ACTIVE_ARCH=NO
     env: 
       destination: ${{ matrix.destination }}
{{< / highlight >}}

Finally, in the last step the tests created in the application are executed:
{{< highlight swift >}}
 - name: Test
   run: |
           xcodebuild clean test -project GHFollowers.xcodeproj -scheme GHFollowersTests -destination "${destination}" CODE_SIGN_IDENTITY="" CODE_SIGNING_REQUIRED=NO ONLY_ACTIVE_ARCH=NO
   env: 
          destination: ${{ matrix.destination }}
{{< / highlight >}}

When we upload code to the repository, this workflow will run. If we access the Actions tab of the repository again, we can see a list of the times the workflow has been executed. In this case, if no error has occurred, we obtain the following:
{{< image src="images/post/cicd_github_actions_6.png">}}
We can prove what happens when one of the tests fails. By changing one of the tests so that it is not met, you get:
{{< image src="images/post/cicd_github_actions_7.png">}}
Where we can see what test has failed.