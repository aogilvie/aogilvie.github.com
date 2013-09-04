---
layout: post
author:
    name: Ally
    url: https://twitter.com/allyogilvie
title:  "Updating a Project To CDV 3.0 Part2"
categories: blog
tags: android, ios, cordova
---

# Updating a Project to Cordova 3.0
------

### Part 2 - Android

As mentioned previously:

This is a quick tutorial on creating a Cordova 3.0 project without CLI. It might be that you are upgrading an existing bulky project or whatever. Currently if you want to use Cordova 3.0 or any CLI you have to a make new project from scratch (using CLI)… 

![image](/images/noo.jpg)

However, this tutorial will show you how to make or update to Cordova 3.0.
	
![image](/images/ooo.gif)

Here is a quick step-by-step starting with a completely empty project (check what you need to change for your existing project). **For iOS** see [part 1](/blog/2013/09/04/updating-a-project-to-cordova-3.0.html).

# Android
------

- Create a new Android project. For this tutorial i'm using Android Stuido, you don't have too, but i'll consider you clinically insane if you use Eclipse…
- Create a ```libs``` folder at project root and add Cordova 3.0 jar to your project.
	For the purpose of this tutorial, [here's something I made earlier](https://github.com/Wizcorp/cordova-binaries/tree/v3.0/android).
- Create an ```assets``` at project root, then a ```www``` folder in side that. 
- Add the following files to ```www``` using Android Studio or your local file/window client (Finder / Windows Exporer);

**index.html** 

Something like this;
	
	<!DOCTYPE html>
	<html>
		<head>
			<title></title>
    		<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no;" />
			<meta charset="utf-8">
			<script type="text/javascript" charset="utf-8" src="cordova.js"></script>
    		<script type="text/javascript">
			function onBodyLoad() {
				document.addEventListener("deviceready", onDeviceReady, false);
			}
			function onDeviceReady() {
    		    alert("Awesome, Cordova is ready to rock!");
			}
    		</script>
    	</head>
    	<body onload="onBodyLoad()">
     		<p>A simple index file.</p>
     	</body>
	</html>

**cordova.js**

from [https://github.com/apache/cordova-android/blob/3.0.x/framework/assets/www/cordova.js](https://github.com/apache/cordova-android/blob/3.0.x/framework/assets/www/cordova.js)

**cordova_plugins.js** 

*OPTIONAL* - If you have plugins, create a file that looks like;
	
	cordova.define('cordova/plugin_list', function(require, exports, module) {		module.exports = [
		{
    	   	"file": "<file path to your plugin.js relative to /www/>",       			"id": "<an id for your plugin e.g. jp.ally.greatPlugin>",       			"clobbers": [
     	 		"window.greatPlugin"
			]
		}]
	});

In Android Studio it's time to add a little bit of Java;

**mainActivity.java** (or whatever you have call your new project)

Your main activity class should ```extends DroidGap``` thus you will be prompted to add ```import org.apache.cordova.DroidGap```

Next in the ```onCreate``` override make sure to have the lines below;

	super.onCreate(savedInstanceState);
    super.loadUrl("file:///android_asset/www/index.html");
        
**config.xml**

Add **config.xml** to ```res/xml/``` (create ```xml``` directory if not existing)

Here is a sample config.xml;

	<?xml version='1.0' encoding='utf-8'?>
	<widget id="io.cordova.helloCordova" version="1.0.0" xmlns="http://www.w3.org/ns/widgets">
    	<name>Hello Cordova</name>
    	<description>
    	A sample Apache Cordova application that responds to the deviceready event.
    	</description>
    	<author email="dev@cordova.apache.org" href="http://cordova.io">
        Apache Cordova Team
    	</author>
    	<content src="index.html" />
    	<feature name="LocalStorage">
        	<param name="ios-package" value="CDVLocalStorage" />
        </feature>
    	<access origin="*" />
    	<preference name="KeyboardDisplayRequiresUserAction" value="true" />
    	<preference name="SuppressesIncrementalRendering" value="false" />
    	<preference name="UIWebViewBounce" value="true" />
    	<preference name="TopActivityIndicator" value="gray" />
    	<preference name="EnableLocation" value="false" />
    	<preference name="EnableViewportScale" value="false" />
    	<preference name="AutoHideSplashScreen" value="true" />
    	<preference name="ShowSplashScreenSpinner" value="true" />
    	<preference name="MediaPlaybackRequiresUserAction" value="false" />
    	<preference name="AllowInlineMediaPlayback" value="false" />
    	<preference name="OpenAllWhitelistURLsInWebView" value="false" />
    	<preference name="BackupWebStorage" value="cloud" />
    	<preference name="fullscreen" value="true" />
    	<preference name="webviewbounce" value="true" />
	</widget>

*OPTIONAL* - If you have plugins add to the config.xml inside the ```<widget>``` tag (just aobve ```<access origin="*" />``` is fine):

(Below assumes the plugin class is GreatPlugin.m and your Cordova.exec will call "GreatPlugin")

    <feature name="GreatPlugin">
        <param name="ios-package" value="GreatPlugin" />
        <param name="onload" value="true" />
    </feature>
