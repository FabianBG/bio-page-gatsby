---
title: Three days of Ionic, React Native and Flutter
date: "2020-02-27T22:40:32.169Z"
template: "post"
draft: false
slug: "three-days-ionic-reactnative-and-flutter"
category: "Side Project"
tags:
  - "React Native"
  - "Ionic"
  - "Flutter"
  - "Mobile development"
description: "Another comparision of these 3 frameworks of hybrid mobile development with a litle and messy sample."
socialImage: "https://miro.medium.com/max/800/1*9tmBZ0TonJyJ26sj1bc_JA.png"

---
# Intro

As developers, We always have the difficult desition to find the right tool to fit customer expectations, in mobile development actually exist a lot of ways to achieve the desired results but wich one you have to use, if you go to internet you found a lot of articles about wich is better and an endless fight for the best one and defining it as the silver bullet framework, but as always the best way to be sure of the pros of any technology is better to give a try. That is what I decided to do a little experiment on this 3 frameworks to understand better each one.
This article is not about wich one is better is about to identify the best qualities of each one and in wich cases are better.


# Project definition

As a simple test I defined a simple application with a native integration to the camera, so the project is composed of four views, a native integration to read a qr code, JSON API service consumption and a view wich renders a long list of items.

## Views

* Item list: The main view wich one get data from a custom service and show 10 or the full list with a toggle button to show all or just 10.

* Item form: a very simple form to insert a new item, wich redirects to item list and shows a modal with the id of a new item.

* Item detail: A detail view of the item wich one consumes a get one service.

* QR scaner: An integration with the device camera to read a qr code with an item id, after it succedd it redirect to item detaill view.

## Day one - React Native

I decided to start with react native in wich one I have more experience, to develop for react native you should know javascript and for keeping things simple this sample app do not use the full react native stuff, like no redux no sagas just simple react.

### Development

To star a new project refer to the official page wich has all the requirements. (react native page)[https://facebook.github.io/react-native/docs/getting-started]

I wanted to use type script so for the initialization of the project with ts support:

`react-native init AwesomeTSProject --template react-native-template-typescript`

And to run the project directly on an android device to start the development:

`react-native run android`

React native supports hot reloading and remote debug just shake the phone and see how the magic happen.

In this project I used these libraries:

* React navigation to move between views for the installation I used the next commands: 

`npm install @react-navigation/native @react-navigation/stack`

`npm install react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view`

For the complete guide of installation and device specific configurations refer to the official page. [https://reactnavigation.org/docs/en/getting-started.html]

QR code scanner library to use the device camera and scan qr codes, for the installation I used the next commands: 
`npm install react-native-camera --save`

`npm install react-native-qrcode-scanner --save`

For more documentation refer to this page [https://github.com/moaazsidat/react-native-qrcode-scanner]

#### Code repo
The full code of the sample can be founded in this repo:
[https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/tree/master/simpleQR_reactNative]

#### App

![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/reactNative/rn-showcase.png?raw=true)
 

## Day two - Ionic

The second day of work is for Ionic; I had previous experiences on Ionic with angular, but I realized Ionic has changed a lot as angular changed too, they added new native integration engine named capacitor and a bunch of things, so for the Ionic development is the same javascript requirement of knowledge. As the react native app the same applies, a very simple app with the basic libraries. 

### Development
To install ionic and start a new project refer to the official documentation in their web page (ionic page)[https://ionicframework.com/docs/angular/your-first-app]

Ionic is not just a mobile framework it work as a multiplatform this is important because for the mobile support it is necessary to use native integration engine, in this case I am going to use the new one.

To configure or to add a mobile platform do the next config:

`ionic build`

`npx cap add android`

And finally you can develop the project in the web browser if you don't have any native component usage with:

`ionic serve`

And if you want to install the app on a device like an android


`ionic capacitor run android`


For the qr reader I used bar code scanner wich can scan qr codes too, for to install it:

`ionic cordova plugin add phonegap-plugin-barcodescanner`

`npm install @ionic-native/barcode-scanner`

This plugin has compatibility with capacitor, so it can run with the new engine.


For ionic the routing library is already included, in this case angular router, you can choose form vue and react to start an ionic project. We can use the client ionic to create pages, components and services fore more info refer to [https://ionicframework.com/docs/cli/commands/generate]

### Code repo

The full code repo of this sample is on this url [https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/tree/master/simpleQR_ionic]

### App

![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/ionic/io-showcase.png?raw=true)

## Day three - Flutter
And finally for the last day the new boy in the neightboir, I don't know anything about dart of flutter so in this project I am going to keep very simple and just the necesary not complex architectures.

### Development

To create a new project I refered to the oficial page (flutter page)[https://flutter.dev/docs/get-started/install]
Install all languaje support of dart before start coding an maybe some dart quickstart. 


To star the project on a device use this command:

`flutter run`

### Development
For this development we have to include the http libray an injectable just to keep things clean.
To add dependencies to a flutter project add the library to the pubspec.yaml

```
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^0.1.2
  http: any
  provider: ^4.0.2
  injectable: ^0.1.0
  get_it: ^3.1.0
  qrscan: ^0.2.17

dev_dependencies:
  flutter_test:
    sdk: flutter
  build_runner:
  injectable_generator: ^0.1.0
```
Then execute:

`flutter pub get`


An for the qr scaner I installed a plugin fore more info refer to it page [https://pub.dev/packages/qrscan]

### Code repo
The full code repo of this sample is on this url
[https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/tree/master/simple_qr_flutter]

## App
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/flutter/fl-showcase.png?raw=true)


## Results

At the end of these three days I am writing this article, I enjoyed the app development on the diferent ways, and all the three frameworks has it own strongest points and use cases.


### Development
In the development all start with the installation where all of them have a lot of information about it and not so hard, the hardest part is to configure the android or ios environment after that it is very simple. The create a new project part could be a bit different for each one but everyone has it own client and project generator.

So how hard is to develop in the react native project, all the basic app it took me about an average of 5 hours, I faced some issues with the navigation router installation and in the design of the app, I have to admit, I am not a very good designer. React has his own component for the visuals so not working with html and css can be a hard part, but react native has a lot of third party design frameworks wich provides a bunch of well designed components.

On the Ionic project the full app took me like an average of 4 hours in total more simple than react native and flutter (maybe because I know more JavaScript than dart) the designs are simpler because Ionic has a set of prebuilt components and is easy to design over html and css. The hardest part is the integration with capacitor because is new and has it complications, if you are usisng cordova maybe the same it has it owns complications.

At the end flutter was interesting to learn, dart and the way the flutter development flow was an fascinating way to create an app, the design it is a kind of different no html and css neither JSX just code, but if you like to design with code flutter is your framework. At the end It took me like an average of 7 hours, but I someway it was a new languaje for me. Flutter in the library import gave me an easy time and in the android integration too.


All of them support hot reloading on a real device or emulator and a good console log for print some debug stuff. The JSON API consumption was easy in react and ionic but in dart a kind of frustrating the json mapping to dart objects.


### Performance
To measure the performace I used the Android Studio profiler tool, it provides a bunch of very interesting metrics. For all the testing I used a Xiaomi mi9 lite.

First of all, each framework has it own ways to improve the performace with a little twerks and stuff but requires a lot of fine tuning and at the end every one of them can perform better but it is not like the performance is going to incease substantially.

The first impression to me to ses how well performant is the app is the launching time, the time the app took to be ready to use, so flutter is the quicker one, then is react and at the end is ionic with the slowest start up.

For each app I get a full test cicle, wich is:

* Open the app
* Show all items of the list
* Scroll and tap to the detail view
* Go to new view and add a new item
* Go to scan view and scan a qr code

### React native results

#### CPU
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/reactNative/rn-cpu.png?raw=true)

#### Memory
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/reactNative/rn-mem.png?raw=true)

#### Energy
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/reactNative/rn-energy.png?raw=true)


### Ionic Results

#### CPU
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/ionic/io-cpu.png?raw=true)

#### Memory
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/ionic/io-mem.png?raw=true)

#### Energy
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/ionic/io-energy.png?raw=true)

### Flutter results

#### CPU
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/flutter/fl-cpu.png?raw=true)

#### Memory
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/flutter/fl-mem.png?raw=true)

#### Energy
![Alt text of image](https://github.com/FabianBG/three-days-with-ionic-reactNative-flutter/blob/master/docs/flutter/fl-energy.png?raw=true)


## Conclusions
At the end the three frameworks offer what they promised a hybrid mobile development with easy integration to both main mobile platforms with an unified codebase. All of them has a decent performance on this simple app, and the development was a kind of easy but if you don't know dart it is going to be a slow start.

At the memory level flutter like everybody says, has the best performance, followed by react native and at the end ionic. 

At cpu usage react has the biggest usage, of cpu maybe for the native render and ionic because all of the render was by the web view, and flutter very good perform ae at cpu level even if has to use native components. 

The energy usage for ionic is the lowest wich means than the web view render consumes less battery than a native rendering.

I feel like flutter has a little bit of slow performance on the JSON API requests, maybe the mapping of json to dart objects or the fast view rendering of flutter is quicker than the request response.

At the development level the ionic way of develop a mobile app is like you are developing a web app and I think it is a kind of wrong, because the behavior of a mobile app it is different of a web app. So in React Native and flutter the architecture of state management out of the box is more helpful.

So which is the best? All of them has their own strengths and in my opinion and based on my past experiences these are my final thoughts:

* Ionic: Very easy and flexible framework for small and a kind of informative apps, when you don't have many resources to do a specialised mobile development and need to create a mobile app with an existing web app as main concept. So you can easily get you mobile app reusing the web elements. And the final mobile app is just a helpful tool for you business.

* React Native: Scales in complexity over Ionic, it requires a bit more effort than Ionic because it handles other concepts, if you are a react web developer it is easier to jump to react native, but the main difference is the component design it different form the traditional html and css it tries to keep the css attributes and html tags but wit native components so the design change and it took a bit of more effort, but with a better performance and recommended for applications with more responsibilities than can be the core of the business. It requires more resources and efforts for a team to develop an app but with more better user experience at the end.

* Flutter: The new one who cames with a lot of advantages and improvements, as the case of react native you can use this for an app with can be the core business, but keep in mind the dart learning curve and the small community, flutter is pretty new but with a very high potential. I specially recommend dart for starter projects than are going to grow to be complete mobile apps, and for resources it requires more than the other two because flutter requires a kind of more time due to the new things and changing ecosystem than surround it framework.


