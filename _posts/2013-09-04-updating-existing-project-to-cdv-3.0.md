---
layout: post
author:
    name: Ally
    url: https://twitter.com/allyogilvie
title:  "Updating a Project To CDV 3.0"
categories: blog
tags: android, ios, cordova
---

# Updating a Project to Cordova 3.0
------

This is a quick tutorial on creating a Cordova 3.0 project without CLI. It might be that you are upgrading an existing bulky project and the thought of starting again… 

![image](/images/noo.jpg)

However, get your project upto Cordova 3.0 then adding plugins or updating plugins is as easy as…

	cordova plugin add < URL || pathToDirectory >
	
![image](/images/ooo.gif)

Here is a quick step-by-step starting with a completely empty project. Let's do iOS first. **For Android** see part 2 (link coming soon…).


# iOS
------

- Create a new Xcode 'empty' application project.
- Add Cordova 3.0 framework or source code to your project.
	For the purpose of this tutorial, [here's something I made earlier](https://github.com/Wizcorp/cordova-binaries/tree/v3.0/ios/bin/Debug-iphoneos).
- Add AssetsLibrary.frameworkm MobileCoreServices.framework, CoreLocation.framework
- Add our ```www``` folder. (You can do this in Finder, then drab into Xcode selecting; 'Create folder references…')
- Back in Finder add to the ```www``` the following files;

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

from [https://github.com/apache/cordova-ios/blob/3.0.x/CordovaLib/cordova.js](https://github.com/apache/cordova-ios/blob/3.0.x/CordovaLib/cordova.js)

**cordova_plugins.js** 

*OPTIONAL* - If you have plugins, create a file that looks like;
	
	cordova.define('cordova/plugin_list', function(require, exports, module) {		module.exports = [
		{
    	   	"file": "<file path to your plugin.js relative to /www/>",       			"id": "<an id for your plugin e.g. jp.ally.greatPlugin>",       			"clobbers": [
     	 		"window.greatPlugin"
			]
		}]
	});

Back into Xcode. Create a **MainViewController** class and add the following;

**MainViewController.h**
		
	#import <Cordova/CDVViewController.h>
	#import <Cordova/CDVCommandDelegateImpl.h>
	#import <Cordova/CDVCommandQueue.h>
	
	@interface MainViewController : CDVViewController
	@end
	
	@interface MainCommandDelegate : CDVCommandDelegateImpl
	@end
	
	@interface MainCommandQueue : CDVCommandQueue
	@end


**MainViewController.m**

	#import "MainViewController.h"
	
	@implementation MainViewController
	
	- (id)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil {
	    self = [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
	    if (self) {
	    }
	    return self;
	}
	
	- (id)init {
	    self = [super init];
	    if (self) {
	    }
	    return self;
	}
	
	#pragma mark View lifecycle
	
	- (void)viewWillAppear:(BOOL)animated {
		[super viewWillAppear:animated];
	}
	
	- (void)viewDidLoad {
	    [super viewDidLoad];
	}
	
	- (void)viewDidUnload {
	    [super viewDidUnload];
	}
	
	- (BOOL)shouldAutorotateToInterfaceOrientation:(UIInterfaceOrientation)interfaceOrientation {
		return [super shouldAutorotateToInterfaceOrientation:interfaceOrientation];
	}
	
	#pragma mark UIWebDelegate implementation
	
	- (void)webViewDidFinishLoad:(UIWebView *)theWebView {
	    // Black base color for background matches the native apps
	    theWebView.backgroundColor = [UIColor blackColor];
	    return [super webViewDidFinishLoad:theWebView];
	}
	
	@end
	
	@implementation MainCommandDelegate
	
	#pragma mark CDVCommandDelegate implementation
	
	- (id)getCommandInstance:(NSString *)className {
	    return [super getCommandInstance:className];
	}
	
	- (BOOL)execute:(CDVInvokedUrlCommand *)command {
	    return [super execute:command];
	}
	
	- (NSString *)pathForResource:(NSString *)resourcepath {
	    return [super pathForResource:resourcepath];
	}
	
	@end
	
	@implementation MainCommandQueue
	
	- (BOOL)execute:(CDVInvokedUrlCommand *)command {
	    return [super execute:command];
	}
	
	@end


**MainViewController.xib**

In Xcode, File > New > File … Select 'User Interface', then View file. Name it **MainViewController**
	 
Now modify the default **AppDelegate** class

**AppDelegate.h**

Add / modify the following;

	#import <Cordova/CDVViewController.h>
	
	@interface AppDelegate : NSObject <UIApplicationDelegate>{}
	
	@property (nonatomic, strong) IBOutlet UIWindow* window;
	@property (nonatomic, strong) IBOutlet CDVViewController* viewController;
	
	@end

**AppDelegate.m**

Add / modify the following;

	#import "MainViewController.h"
	
	- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
	    CGRect screenBounds = [[UIScreen mainScreen] bounds];
	    
	    self.window = [[UIWindow alloc] initWithFrame:screenBounds];
	    self.window.autoresizesSubviews = YES;
	    
	    self.viewController = [[MainViewController alloc] init];
	    self.viewController.wwwFolderName = @"www";
	    self.viewController.startPage = @"index.html";
	    
	    // NOTE: To customize the view's frame size (which defaults to full screen), override
	    // [self.viewController viewWillAppear:] in your view controller.
	    
	    self.window.rootViewController = self.viewController;
	    [self.window makeKeyAndVisible];
	    
	    return YES;
	}

**config.xml**

Finally add your config.xml, here is a sample;

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


## Part 2 - Android

Link (coming soon...)


# Troubleshooting
------


#### ```-[UIViewController _loadViewFromNibNamed:bundle:]``` loaded the "MainViewController" nib but the view outlet was not set.

Click on **MainViewController.xib**, click File's Owner, press cmd+option+3, check that the Class is "MainViewController".

Press cmd+option+6, click and drag from "view" to the left hand column to select "View".



