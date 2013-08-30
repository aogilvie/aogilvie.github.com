---
layout: post
author:
    name: Ally
    url: https://twitter.com/allyogilvie
title:  "Cordova Biniaries Released"
categories: blog
tags: phonegap
---

Yesterday I released a new repository that will be populated with native projects to help compile your own Cordova/Phonegap binary / library. So far I have released iOS binaries and template framework maker projects for Cordova versions 2.7 & 3.0, more will follow soon as well as Android.

There were several reasons for doing this;

- I LOVE libs/.frameworks just like JavaScript guys love their blah.js :)

- From Cordova 3.0 major "features" are now plugins - I think rightly so, but installing for the average user means using the Cordova CLI tools. However it is still easy to customise the right Cordova binary for you without CLI tools using this repository. IF you want Cordova and the camera plugin in a framework, you can make that.

- In the day to day world that I work in (sort of middleware department), I need to provide Cordova support to X amount of games and X developers. Things are easier for me to ship the "kitchen-sink" version of Cordova and not worry about those individual game feature requirements. So long as I spread aweness of for example, the permissions settings on Android the game devs can use any of Cordova's APIs on demand. 
As a developer I may only ever use 70% of APIs in a library, but that should not be too much of a problem. IF I never use the other 30%, then that is a flaw in the SDK design (that 30% should probably be a different product). Thankfully Cordova's "core plugins" are quite necessary for a lot of our projects.


Check it out: [https://github.com/Wizcorp/cordova-binaries](https://github.com/Wizcorp/cordova-binaries)

