---
layout: post
author:
    name: Ally
    url: https://twitter.com/allyogilvie
title:  "Basic Authentication with Android Webkit"
categories: blog
tags: android
---

#Basic Authentication with Android Webkit

A common simple solution to keeping HTML5 games private while in development, but allowing access for testing is using [basic authentication](http://en.wikipedia.org/wiki/Basic_access_authentication). Publish your HTML5 app on Testflight, your client can download and check out your app securely over a public network.

Cool.

Until, you want to have application assets loaded from multiple domains or sub-domains into your application with basic authentication access.

There is a bug in android.webkit.WebViewClient...

###Example

- My game is hosted at ```http://game.domain.com/folder/pages/```
- My images are hosted at ```http://game-images.domain.com/images/```

The are many reasons why you might host assets in another location (a dedicated server - services like [Akamai](http://www.akamai.com/), are very popular right now for this) to spread the load off your main game server.

So let's say you load a JavaScript file with ```src="http://user:password@game.domain.com/folder/pages/"```, Webkit will grab your user and password from the source and use it to load your JavaScript - so far so good!

Next, we preload an image using ```src=http://user:password@game-images.domain.com/images/image.png```, Webkit catches the request to load a web resource but completely ignores the user credentials in the URL! - Whut?

###Prove it!

So there are two interesting methods to observe this behaviour;

	public android.webkit.WebResourceResponse shouldInterceptRequest(android.webkit.WebView view, 
		java.lang.String url) { }

and

	public void onReceivedHttpAuthRequest(android.webkit.WebView view, 
		android.webkit.HttpAuthHandler handler, java.lang.String host, java.lang.String realm) { } 

Using ```shouldInterceptRequest()``` we can check what the URLs look like when webkit tries to load them.

Here is an example of ```shouldInterceptRequest``` and some logs from an application I made earlier;


    @Override
    public android.webkit.WebResourceResponse shouldInterceptRequest(android.webkit.WebView view, 
    	java.lang.String url) {
        // Check for authentication in url and set if necessary
        try {
                URL url1 = new URL(url);
                Log.d("DEBUG", "Check host: " + url1.getHost() + 
                	" has user info: " + 
                	url1.getUserInfo() + 
                	" for url: " + url);
                String userInfo = url1.getUserInfo();
                if (userInfo != null) {
                    String[] credentials = userInfo.split(":");
                    Log.d("DEBUG", "Setting credentials for host: " + getBaseDomain(url));
                    view.setHttpAuthUsernamePassword(getBaseDomain(url), 
                    	"Restricted", credentials[0], credentials[1]);
                }
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        return super.shouldInterceptRequest(view, url);
    }

[All URL host names have been redacted.]

	D/DEBUG: Check host: <subdomain>.<domain>.jp has user info: user:password for url: http://user:password@<subdomain>.<domain>.jp/app/game/
	
	D/DEBUG: Setting credentials for host: <domain>.jp
	
	D/DEBUG: Check host: <subdomain>.<domain>.wizcorp.jp has user info: null for url: http://<subdomain>.<domain>.jp/assets/img/default/ui/landing.jpg
	
	D/DEBUG: Check host: <subdomain>.<domain>.wizcorp.jp has user info: null for url: http://<subdomain>.<domain>.jp/assets/img/default/ui/btn_red.png
	
	D/DEBUG: Check host: <subdomain>.<domain>.wizcorp.jp has user info: null for url: http://<subdomain>.<domain>.jp/assets/img/default/ui/motif_6.png
	
	D/DEBUG: Check host: <subdomain>.<domain>.wizcorp.jp has user info: null for url: http://<subdomain>.<domain>.jp/app/game/landing?language=en
	...
	
As you can see only the first URL to be caught by ```shouldInterceptRequest()``` contains the credentials in the URL. **Every subseqent URL however, has had its credentials extracted!** 
	
The next method to look at is ```onReceivedHttpAuthRequest()``` using this we can force Webkit to achnoledge our basic authentication credentials when it receives an authentication challenge.

	@Override
    public void onReceivedHttpAuthRequest(android.webkit.WebView view, 
    	android.webkit.HttpAuthHandler handler, java.lang.String host, java.lang.String realm) {
        Log.d"DEBUG", "onReceivedHttpAuthRequest host : " + host);
        String[] credentials = view.getHttpAuthUsernamePassword(host, realm);
        if (credentials != null) {
            Log.d("DEBUG", "Setting credentials for user : " + credentials[0]);
            handler.proceed(credentials[0], credentials[1]);
        } else {
            Log.d("DEBUG", "No credentials found");
        }
    }
    
Here we can see that when a challenge is made, we can use ```view.getHttpAuthUsernamePassword()``` to get the user and password that we set earlier in the previous method and pass that to the ```HttpAuthHander```.

The devil is in the detail, we actually use a private method ```getBaseDomain()``` in ```shouldInterceptRequest()``` before, to set the credentials for all the things on <domain>.jp. Unfortunately, if you have different user and password authentication on separate sub domains this technique will not work :(