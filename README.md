# Apache Cordova Facebook Plugin
by [Mayur Panchal](http://www.excellentwebworld.com)



The Facebook plugin for [Apache Cordova](http://incubator.apache.org/cordova/) allows you to use the same JavaScript code in your Cordova application as you use in your web application. However, unlike in the browser, the Cordova application will use the native Facebook app to perform Single Sign On for the user.  If this is not possible then the sign on will degrade gracefully using the standard dialog based authentication.

* Supported on PhoneGap (Cordova) v2.1.0 and above.

## Facebook Requirements and Set-Up

The Facebook SDK (both native and JavaScript) is changing independent of this plugin. The manual install instructions include how to get the latest SDK for use in your project.

To use this plugin you will need to make sure you've registered your Facebook app with Facebook and have an APP_ID (https://developers.facebook.com/apps).

If you plan on rolling this out on iOS, please note that you will need to ensure that you have properly set up your Native iOS App settings on the [Facebook App Dashboard](http://developers.facebook.com/apps). Please see the [Getting Started with the Facebook SDK](https://developers.facebook.com/docs/getting-started/facebook-sdk-for-ios/3.1/): Create a Facebook App section, for more details on this.

If you plan on rolling this out on Android, please note that you will need to [generate a hash of your Android key(s) and submit those to the Developers page on Facebook](https://developers.facebook.com/docs/getting-started/facebook-sdk-for-android/3.0/) to get it working. Furthermore, if you are generating this hash on Windows (specifically 64 bit versions), please use version 0.9.8e or 0.9.8d of [OpenSSL for Windows](http://code.google.com/p/openssl-for-windows/downloads/list) and *not* 0.9.8k. Big ups to [fernandomatos](http://github.com/fernandomatos) for pointing this out!

# Project Structure

<pre>
  |_example
  |  |_Simple
  |  | |_index.html
  |  |_HackBook
  |    |_index.html
  |    |_README
  |    |_hackbook.manifest
  |    |_img
  |    |_css
  |    |_js
  |_test
  | |_pg-plugin-fb-connect-tests.js
  |_src
  | |_android
  | | |_ConnectPlugin.java
  | | |_facebook
  | |_ios
  |   |_FacebookConnectPlugin.m
  |   |_FacebookConnectPlugin.h
  |   |_FacebookSDK.framework
  |_www
  |  |_pg-plugin-fb-connect.js
  |  |_facebook-connect-debug.js
</pre>


`www/facebook-connect-debug.js` is the modified facebook-js-sdk. It already includes the hooks to work with this plugin.

`www/pg-plugin-fb-connect.js` is the JavaScript code for the plugin, this defines the JS API.

`src/android` and `src/ios` contain the native code for the plugin for both Android and iOS platforms. They also include versions of the Android and iOS Facebook SDKs. These are used during automatic installation. During manual installation, you are encouraged to download the most recent versions of the Facebook SDKs for you projects. 


## Adobe PhoneGap Build

If using this plugin on Adobe PhoneGap Build you can ignore the instructions below and go straight to the 
PhoneGap Build documentation available [here] (https://build.phonegap.com/docs/plugins#facebookconnect).


## Phonegap 3.1.0 Installation

   
**This is almost ready to install this plugin automagically. It still requires a few parts until a few bugs are fixed.**

1. Installed the plugin using phonegap plugin tools


```
phonegap local plugin add https://github.com/mayurloved/phonegap-facebook-integration.git --variable APP_ID="[FB APP ID]" --variable APP_NAME="[FB APP NAME]"
```
    
2. Add the FacebookSDK.framework to your project manually by dragging the FacebookSDK.framework folder on to your frameworks folder

    *Requires the following to fixed before this can be automated*

    http://stackoverflow.com/questions/18653413/how-to-copy-a-custom-ios-framework-using-plugin-xml-on-phonegap-3

3. Replace the $APP_ID and $APP_NAME variables in your *-Info.plist with your equivalent set above (Not quite sure why this isn't working automatically at the moment) and be sure to check NSMainNibFile keys for blank strings as this will break the app on launch.

    *Requires the following to fixed before this can be automated*

    http://stackoverflow.com/questions/18618001/cordova-3-0-plugin-plist-config

4. Add the following to your config.xml

```
<feature name="FacebookConnect">
    <param name="ios-package" value="FacebookConnectPlugin" />
</feature>
```

Thats it. This version mirrors the current web javascript sdk provided by Facebook. This makes life easy when you want to use the same code between your web version and phonegap. The Facebook SDK will be pulled in with your include of phonegap.js

```JavaScript
if (phonegap.isEnabled()) {
  document.addEventListener('deviceready', function () {
    FB.init({
      appId: FBAPPID,                 // App ID from the app dashboard
      channelUrl: '//localhost/channel.html',        // Channel file for x-domain comms
      nativeInterface: CDV.FB,
      status: true,
      frictionlessRequests: true,
      useCachedDialogs: false
    });
  }, false);
} else {
  window.fbAsyncInit = function() {
    FB.init({
      appId: FBAPPID,                        // App ID from the app dashboard
      channelUrl: 'http://localhost/channel.html', // Channel file for x-domain comms
      status: true,                                 // Check Facebook Login status
      frictionlessRequests: true,
      useCachedDialogs: false
    });
  };

  // Load the SDK asynchronously
  (function(d, s, id){
    var js, fjs = d.getElementsByTagName(s)[0];
    if (d.getElementById(id)) {return;}
    js = d.createElement(s); js.id = id;
    js.src = "//connect.facebook.net/en_US/all.js";
    fjs.parentNode.insertBefore(js, fjs);
  }(document, 'script', 'facebook-jssdk'));
}
```

## Manual Android Installation

1. [Create a basic Cordova Android application](http://docs.phonegap.com/en/2.5.0/guide_getting-started_android_index.md.html#Getting%20Started%20with%20Android).
 * NOTE: Min Target has to be set to 8. The plugin has an issue if you set your minimum target higher than that. You can edit this in your Android Manifest file. Set the Project Build Target to at least 11 if you see Android Manifest errors related to newer attributes that have been added in 2.2.0.
 
2. In the Cordova Android application you will need to put the following in your `res/xml/config.xml` file as a child to the plugin tag: <pre>&lt;plugin name="org.apache.cordova.facebook.Connect" value="org.apache.cordova.facebook.ConnectPlugin" /&gt;</pre>

3. You'll need to set up the Facebook SDK for Android:
  * [Install the Facebook SDK for Android and the Facebook APK](https://developers.facebook.com/docs/getting-started/facebook-sdk-for-android/3.0/)
  * [Import the Facebook SDK into Eclipse](https://developers.facebook.com/docs/getting-started/facebook-sdk-for-android/3.0/)
  * Link the Facebook SDK library to your project.  View the properties for the project, and navigate to the 'Android' tab. In the lower part of the dialog, click 'Add' and choose the 'FacebookSDK' project from the workspace.
  * Add a new `com.facebook.LoginActivity` activity to your app to handle Facebook Login. Open up your `AndroidManifest.xml` file and add this additional activity:

            <activity android:name="com.facebook.LoginActivity"
                  android:label="@string/app_name" />

4. From the Cordova Facebook Plugin folder copy ConnectPlugin.java from `src/android/` folder into the root of your Cordova Android application into `src/org/apache/cordova/facebook/`. You may have to create that directory path in your project. 

5. From the Cordova Facebook Plugin folder copy the `www/cdv-plugin-fb-connect.js`, `www/facebook_js_sdk.js` and `example/HackBook/` files into your application's `assets/www` folder. Overwrite the existing index.html file.

6. Replace your appId in the new index.html file. Leave the quotes.

Now you are ready to create your application! Check out the `example` folder for what the HTML, JS etc looks like.

You can run the application from either the command line (`ant clean && ant debug install`) or from Eclipse.

## Manual iOS Installation (Mac OS X)

NOTE 1: If you are having problems with SBJSON conflicts, download the latest version of git clone the latest cordova-ios code, build the installer, and run the installer to get updated!

NOTE 2: If you're upgrading from SDK 3.0 to 3.1 in the iOS6, you can't ask for both read and write permissions when the user is authenticating. If you do this you'll get a `com.facebook.sdk error 2` error alert. This happens due to a flow change in the 3.1 SDK. More info about this can be found in the [official docs](https://developers.facebook.com/docs/tutorial/iossdk/upgrading-from-3.0-to-3.1/). Also, make sure you have configured the app's Bundle ID in the Facebook app details, under the "Native iOS App" configuration, otherwise you'll get another `com.facebook.sdk error 2` alert. At least, if some of your earlier authentications failed, the device may turn the app to off in Settings > Facebook > Allow These Apps to Use Your Account, so, make sure your app is allowed.

(To be updated) View the [Video](http://www.youtube.com/watch?v=nVxFGiIoPgk&list=UU-b4-PjK0gq4QDpIpsLiFdg&index=1&feature=plcp)

### Create a Basic Cordova App

Create a basic Cordova iOS application by following the [PhoneGap Getting Started Guide](http://docs.phonegap.com/en/2.5.0/guide_getting-started_ios_index.md.html#Getting%20Started%20with%20iOS)

### Add the Facebook iOS and JavaScript SDK

1. Download the latest Facebook SDK for iOS from the [iOS Dev Center](https://developers.facebook.com/ios/).
2. Add the Facebook SDK for iOS Framework by dragging the **FacebookSDK.framework** folder from the SDK installation folder into the Frameworks section of your Project Navigator.
3. Choose 'Create groups for any added folders' and deselect 'Copy items into destination group's folder (if needed)' to keep the reference to the SDK installation folder, rather than creating a copy.
4. Add the Facebook SDK for iOS resource bundle by dragging the **FacebookSDKResources.bundle** file from the **FacebookSDK.framework/Resources** folder into the Frameworks section of your Project Navigator.
5. As you did when copying the Framework, choose 'Create groups for any added folders' and deselect 'Copy items into destination group's folder (if needed)'
6. Add the headers by dragging the **DeprecatedHeaders** folder from the **FacebookSDK.framework/Versions/A/DeprecatedHeaders** folder into the Frameworks section of your Project Navigator.
7. Choose 'Create groups for any added folders' and deselect 'Copy items into destination group's folder (if needed)'. This adds the headers as a reference.
8. Click on your project's icon (the root element) in Project Navigator, select your **Project**, then the **Build Settings** tab, search for **Other Linker Flags**.
9. Add the value **-lsqlite3.0**
10. Add the value **-ObjC**
11. Click on your project's icon (the root element) in Project Navigator, select your **Target**, then the **Build Phases** tab, then the **Link Binary With Libraries** option.
12. Add the **Social.framework** framework. Make it an optional framework to support pre iOS6 apps.
13. Add the **Accounts.framework** framework. Make it an optional framework to support pre iOS6 apps.
14. Add the **AdSupport.framework** framework. Make it an optional framework to support pre iOS6 apps.
15. Add the **Security.framework** framework. Make it an optional framework to support pre iOS6 apps.

### Add the Cordova Facebook Plugin

1. Locate the **plugins** section of your Project Navigator and create a group "ios". Make sure it is added as a "group" (yellow folder)
2. From the **Cordova Facebook Plugin** folder copy FacebookConnectPlugin.h and FacebookConnectPlugin.m from the **src** folder into the new group "ios".
3. Find the `config.xml` file in the project navigator and add a new entry as a child to the plugin tag:

```xml
<plugin name="org.apache.cordova.facebook.Connect" value="FacebookConnectPlugin" />
```

4. From the **Cordova Facebook Plugin** folder copy the contents of the **www** folder into the **www** directory in Xcode.

### Run the included samples

1. Under the group **Resources**, find your **[PROJECTNAME]-Info.plist**, add a new entry. For the key, add **FacebookAppID**, and its value is your Facebook **APP_ID**
2. Under the group **Resources**, find your **[PROJECTNAME]-Info.plist**, right-click on the file and select **Open As -> Source Code**, add the **URL Scheme** from the section below (you will need your Facebook **APP_ID**)
3. You can quickly test the examples by following the next instructions then mirror the same process for your app.
4. From the **example** folder, copy either the contents of HackBook folder or the Simple folder into your **www** directory in Xcode. Overwrite the original index.html file in your project. For HackBook, overwrite the original css and js folders as well.
5. Make sure the &lt;script&gt; tags are added and are correct in the index.html. This include a tag for cordova-2.5.0.js, facebook_js_sdk.js and cdv-plugin-fb-connect.js.
6. Add your AppID to your index.html. Should be in the callback for the deviceready event. Leave the quotes.
7. Run the application in Xcode.


### iOS URL Whitelist

The Facebook SDK will try to access various URLs, and their domains must be whitelisted in your config.xml under **access**.

You can either add each subdomain separately:

* m.facebook.com
* graph.facebook.com
* api.facebook.com
* \*.fbcdn.net
* \*.akamaihd.net

Or you can allow all domains with (set to this by default):

* \*

### iOS URL Scheme

Make sure you add the scheme to your [PROJECTNAME]-Info.plist (located as one of the files in your Xcode project), substitute [APP_ID] below to the appropriate values (replace those brackets!). This is to handle the re-direct from Mobile Safari or the Facebook app, after permission authorization.

* [**APP_ID**] is the Facebook app id given by Facebook

<pre>
&lt;key&gt;CFBundleURLTypes&lt;/key&gt;
&lt;array&gt;
	&lt;dict&gt;
		&lt;key&gt;CFBundleURLSchemes&lt;/key&gt;
		&lt;array&gt;
			&lt;string&gt;fb[**APP_ID**]&lt;/string&gt;
		&lt;/array&gt;
	&lt;/dict&gt;
&lt;/array&gt;
</pre>

## Automatic Installation
This plugin is based on [plugman](https://git-wip-us.apache.org/repos/asf?p=cordova-plugman.git;a=summary). To install it to your app, simply execute plugman as follows; It does not currently work with plugman at all. WORK IN PROGRESS 

	plugman install --platform [PLATFORM] --project [TARGET-PATH] --plugin [PLUGIN-PATH] --variable APP_ID="[APP_ID]" --variable APP_NAME="[APP_NAME]"
	
	where
		[PLATFORM] = ios or android
		[TARGET-PATH] = path to folder containing your phonegap project
		[PLUGIN-PATH] = path to folder containing this plugin
		[APP_ID] = Your APP_ID as registered on Facebook


### Our Special Thanks 
https://github.com/mallzee/
[![Mayur Panchal](http://excellentwebworld.com/wp-content/uploads/2013/07/logo.png)](http://www.excellentwebworld.com/ "Blogging")