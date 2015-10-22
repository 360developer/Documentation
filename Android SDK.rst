Integration of Android SDK
==========================

Preparation of the project for iOS SDK

Android SDK Migration 2.0.x - 2.1.x
-----------------------------------

As from March 2015 development of 2.0.x SDK stops, there will be no new features developed in the 360dialog SDK 2.0.x. **All new features will be introduced only in SDK v2.1.**

Please find the main major changes for the new 2.1.x version of Android 360dialog SDK:

- **Starting SDK from each activity is not needed anymore**. SDK initialisation moved to Application class of an app.
- **More flexibility for developers**. Now SDK automates less methods and the developer has more control: mainly dispatching overlays and redirecting user to deeplinks. Previously, overlays and deeplinks were automatically triggered by SDK during the initialisation; now there are separate methods to dispatch operations that have direct impact on user interface in app, so the developer can make all in-app preparations before displaying something which is triggered by SDK.
- **SDK is written for full compatibility with the newest available Android API level version**. Each new version of Android system may introduce problems with using deprecated APIs in SDK 2.0, which was available during its development, and may be no longer available in next upcoming Android releases.
- Default Push Notification Sound, Flash and Vibration available per Notifications.

Integration of the 360dialog Libraries
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Apart from the imported libraries a new class has to be added in your project.

To initialise SDK you have to create sub-class of android.app.Application class in your project::

 public class MyApplication extends Application {
 }

Next in order to use it in your application, you need to modify AndroidManifest.xml (check in the section below the full file as well):

Now, as your Application subclass is used by app, you can initialize 360dialog SDK during app initialization process::

 public class MyApplication extends Application {

 @Override

 public void onCreate() {

 super.onCreate();

          D360ConfigContext configContext = new D360ConfigContext();

          D360ConfigContext configContext = new D360ConfigContext();

          configContext.setDebugMode(true); // use it if you want to turn on/off SDK logs in logcat (usefull when debuging with 360dialog team)

         D360Sdk.init(this, configContext);

      }

 }


Creating SDK Configuration File
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Check the changes on the <resources> <string name>.::

 <?xml version="1.0" encoding="utf-8"?>

 <resources>
  <string name="d360_api_key">API-KEY</string> /** the name of the string has to be replaced **/
  <string name="d360_gcm_sender">123456789012</string> /** the name of the string has to replaced **/
 </resources>

Tracking app visibility status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Different changes has to be applied on the Tracking app visibility status::

    import de.d360.android.sdk.v2.D360Sdk;
    public class AllMyActivities extends Activity {
    @Override

    protected void onResume() {

    D360Sdk.onResume(this);
    super.onResume();

    }

    @Override

    protected void onStop() {

    super.onStop();

    D360Sdk.onStop(this);
    }

    @Override

    public void onWindowFocusChanged(boolean hasFocus) {

    D360Sdk.onWindowFocusChanged(this, hasFocus);

    }

    //This is mandatory especially if your main activity
    //is launched with launchType=singleTop
    //or you are launching your main activity using
    //ACTIVITY_FLAG_SINGLE_TOP

    @Override
    protected void onNewIntent(Intent intent) {
    super.onNewIntent(intent);
    D360Sdk.onNewIntent(this, intent);
     }
    }


Required Permissions in the Android Manifest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are few changes in the AndroidManifest.xml that are mandatory to do, please find out in our specific section of our documentation: AndroidManifest.xml example

GCM Setup, Overlays and Deeplink Handling
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Since the way to handle the deeplink (callbacker) change in this version, please check carefully our documentation in this topic: GCM Setup and Deeplink Handling

How app navigation is preserved after clicking on notification?

As Android guidelines describes two methods of handling app opens on notification click, we choose the “Special Activity” method described in docs: http://developer.android.com/training/notify-user/navigation.html#ExtendedNotification, with some exceptions:

- We don’t expect that you will include android:launchMode=”singleTask”, android:taskAffinity=”” and android:excludeFromRecents=”true” configuration in your activity which will be shown to user after clicking notification.
- We will dispatch your main app’s entry point (main activity, this one which is set in your AndroidManifest.xml with action “MAIN” and category “LAUNCHER”).
- Underneath of main activity, there can be saved navigation stack (if your app can preserve it), but always main activity should be visible after clicking on notification (unless this is a notification with deeplink or URL).


Handling UI operations
^^^^^^^^^^^^^^^^^^^^^^
This feature on our SDK was not in the previous 2.0.x based ones. Please check carefully the whole chapter in our documentation: Handling UI operations.


Before integrating
------------------
Before going to integration process, make sure, that every point of list below is checked on your side:

- You have described with 360dialog contact person all events that you want to track.
- You have at least draft of how campaigns should looks like.
- You have in your Google Account (https://console.developers.google.com) you have created project for your application. This project has turned ON “Google Cloud Messaging for Android” (you can do this by going to: APIs & auth > APIs).
- You have enabled “Public API Access” in Google Cloud Console and created API KEY for server applications..
- Be sure, that you have created 360dialog account, you have login, password and you are able to access Administration > App, so you are able to see your API-KEY to use in your app.
- **One API-KEY = one application**. Don’t allow to switch between different 360 dialog API-KEY’s in your application runtime. This will cause many issues with data inconsistency, also may cause SDK to stop working

**If any of step above is not completed on your side, try to accomplish it in first row.** If any of step need to contact with 360 dialog, just contact us to receive access to your 360 dialog web interface.

Importing the Libraries in Android Studio:
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
These are the steps to install 360dialog SDK in the Gradle project:

- In your applications’ project create a folder named „d360sdk“ and copy all 360dialog SDK files into it. Open your “settings.gradle” file, and add an entry „:d360sdk“, so your “settings.gradle” file looks like this::

 include ‘my-application', ':d360sdk'

- Go to your applications’ package and open “build.gradle”. In section dependencies add line::

 compile project(‘:d360sdk’)

*Note:  Place a line pointing to the 360dialog SDK after including the Android support library Rebuild your application to access the SDK files*

Select the option “Sync Project with Gradle Files” to finish importing the 360dialog SDK
Example setting.gradle file::

 include ':production-app', ':d360sdk'

Example piece of build.gradle file::

 dependencies {
   compile fileTree(dir: 'libs', include: ['*.jar'])
   compile 'com.android.support:appcompat-v7:19.+'
   compile project(':d360sdk')
 }


Public class MyApplication
^^^^^^^^^^^^^^^^^^^^^^^^^^
In order to initialise SDK you have to create sub-class of android.app.Application class in your project::

 public class MyApplication extends Application {(
 }

Next, to use it in your application, you need to modify AndroidManifest.xml::

 com.example.app.MyApplication" >

Now, as your Application subclass is used by the app, you can initialise 360dialog SDK during app initialisation process::

 public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        D360ConfigContext configContext = new D360ConfigContext();
        configContext.setDebugMode(true);
        // use it if you want to turn on/off SDK logs in logcat
        // (useful when debugging with 360dialog team)

        D360Sdk.init(this, configContext);
    }
 }


Creating SDK Configuration file
-------------------------------
A configuration file is needed to use the SDK, to establish the connection to 360dialog Back end service and allows sending Push Notifications. You have to add your specific values in res/values/d360.xml.::

 <?xml version="1.0" encoding="utf-8"?>
 <resources>
   <string name="d360_api_key">API-KEY</string>
   <string name="d360_gcm_sender">123456789012</string>
 </resources>

You need to declare the D360RequestService in the AndroidManifest.xml file, to make sure a secured connection to the 360 back end can be established::

 <service
 android:name="de.d360.android.sdk.v2.net.D360RequestService"
 android:exported="false" />

Using Overlays
^^^^^^^^^^^^^^
In order to display 360dialog Overlays, you need to declare a dedicated Activity in the AndroidManifest.xml file::

 <activity
  android:name="de.d360.android.sdk.v2.activities.D360DisplayOverlayActivity"
  android:noHistory="true" />

Tracking app visibility status
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
There are two methods to track the visibility status of your application. You should opt for the second solution if possible, as it is easier to implement and you cannot use both methods at once.

As Android platform does not provide any native callback to obtain app visibility state, we count visible activities by ourselves. As long as there is at least one activity which was resumed, and was not stopped, app is visible. As there were some implementations, which cause activity stop before resuming another one, we started to use filter that should ensure app visibility state. If app is reported as invisible, the filter delays any callbacks and re-checks app visibility state after some  time, to ensure the state is correct. By default, this time is 1 second.

**Method 1: Individual Implementation of app visibility tracking**

If you cannot use method 2 to track visibility status, you have to call the 360dialog SDK methods in the standard activities’ lifecycle methods, as shown in the example::

 import de.d360.android.sdk.v2.D360Sdk;

 public class AllMyActivities extends Activity {

    @Override
    protected void onResume() {
        super.onResume();
        D360Sdk.onResume(this);
    }

    @Override
    protected void onStop() {
        super.onStop();
        D360Sdk.onStop(this);
    }

    @Override
    public void onWindowFocusChanged(boolean hasFocus) {
        D360Sdk.onWindowFocusChanged(this, hasFocus);
    }

    /**
     * This is mandatory especially if your main activity
     * is launched with launchType=singleTop
     * or you are launching your main activity using
     * ACTIVITY_FLAG_SINGLE_TOP
     */
    @Override
    protected void onNewIntent(Intent intent) {
        super.onNewIntent(intent);
        D360Sdk.onNewIntent(this, intent);
    }

 }

**Method 2: Extending your Activities by SDK Activities**

If you use one of the following Activities you can replace them by using the related 360dialog SDK Activities which automatically handle the tracking of the visibility status.

- Replace ``android.app.Activity with de.d360.android.sdk.v2.activities.D360Activity``
- Replace ``android.support.v4.app.FragmentActivity with de.d360.android.sdk.v2.activities.D360FragmentActivity``
- Replace ``android.support.v7.app.ActionBarActivity with de.d360.android.sdk.v2.activities.D360ActionBarActivity`` (available since version 2.1.5build1) replace ``android.support.v7.app.AppCompatActivity with de.d360.android.sdk.v2.activities.AppCompatActivity``

Required Permissions in the Android Manifest
--------------------------------------------
Required Permissions
^^^^^^^^^^^^^^^^^^^^

**Internet permissions**::

 <uses-permission android:name="android.permission.INTERNET" />
 <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

The ``android.permission.INTERNET`` permission is required, so the Android application can send events to the 360dialog back end server.

The ``android.permission.ACCESS_NETWORK_STATE`` checks which kind of connection (if any) is established.

**Write external storage permissions**::

 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

The android.permission.WRITE_EXTERNAL_STORAGE locally stores the 360 appInstanceId for detecting reinstalls.

**Required permissions for Push Notifications**::

 <permission
  android:name="com.example.app.permission.C2D_MESSAGE"
  android:protectionLevel="signature" />
 <uses-permission android:name="com.example.app.permission.C2D_MESSAGE" />
 <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
 <uses-permission android:name="android.permission.WAKE_LOCK" />
 <uses-permission android:name="android.permission.GET_ACCOUNTS" />

*Note: Make sure to replace “com.example.app” with your actual App Name*

Detailed explanation of the permissions can be found at the Google Documentation.

Optional Permissions
^^^^^^^^^^^^^^^^^^^^
::

 <uses-permission android:name="android.permission.READ_PHONE_STATE" />
 <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

The ``android.permission.RECEIVE_BOOT_COMPLETED`` re-registers the user’s GCM Token if there was an update of the OS version.

The ``android.permission.READ_PHONE_STATE`` is required to read SIM card information (ICC, MCC, MNC Carrier Name).::

 <uses-permission android:name="android.permission.VIBRATE" />

Vibrate permission is required in order to use vibration for push notifications. Each push notification can have enabled or disabled vibrations and this can be set in 360 dialog web-console.
Even if vibration is enabled in web-console, device will not vibrate if there is no permission in AndroidManifest.xml.

AndroidManifest.xml example
---------------------------
This is an example of an AndroidManifest.xml file::

 <manifest xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:tools="http://schemas.android.com/tools"
         package="com.example.app"
         android:versionCode="37"
         android:versionName="1.2">

   <uses-sdk
       android:minSdkVersion="9"
       android:targetSdkVersion="22"
       tools:ignore="GradleOverrides"/>

   <!-- GCM Permissions -->
   <permission
           android:name="com.example.app.permission.C2D_MESSAGE"
           android:protectionLevel="signature" />

   <uses-permission android:name="com.example.app.permission.C2D_MESSAGE" />
   <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
   <uses-permission android:name="android.permission.WAKE_LOCK" />
   <uses-permission android:name="android.permission.GET_ACCOUNTS" />
   <!-- END: GCM Permissions -->//

   <!--
           INTERNET and ACCESS_NETWORK_STATE are used for network state checking
           and performing network connection
   -->
   <uses-permission android:name="android.permission.INTERNET" />
   <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

   <!-- Used for writing/reading persisted over installations data. -->
   <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

   <!-- Read Mobile Carrier data when generating hardware report (optional) -->
   <uses-permission android:name="android.permission.READ_PHONE_STATE" />

   <!-- Update GCM Registration Token when system version is changed (optional) -->
   <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

   <!-- Use vibration when receiving push notification (optional) -->
   <uses-permission android:name="android.permission.VIBRATE" />

   <application android:allowBackup="true"
           android:icon="@drawable/launcher_icon"
           android:enabled="true"
           android:label="@string/app_name"
           android:theme="@style/AppTheme" >

 <!-- Replace "ic_dialog360_icon" with your Push Notification icon file (optional) -->
       <meta-data
               android:name="d360_small_notification_icon"
               android:resource="@drawable/ic_dialog360_icon" />

       <activity
               android:name=".LaunchActivity"
               android:icon="@drawable/launcher_icon">
           <intent-filter>
               <action android:name="android.intent.action.MAIN" />
               <category android:name="android.intent.category.LAUNCHER" />
           </intent-filter>
       </activity>

       <activity
               android:name=".WelcomeActivity"
               android:label="@string/activity_welcome_title" />

       <activity
               android:name=".SettingsActivity"
               android:label="@string/activity_settings_title" />

       <!-- SDK Activities -->

       <activity
               android:name="de.d360.android.sdk.v2.activities.D360DisplayOverlayActivity"
               android:noHistory="true" >
       </activity>

       <!-- END: SDK Activities -->

       <!-- GCM Push receiver -->
       <!-- D360 SDK version 2.1.6 and above -->

 <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver" android:exported="true"
           android:permission="com.google.android.c2dm.permission.SEND" >
           <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <category android:name="com.example.app" />
           </intent-filter>
        </receiver>
        <service android:name="de.d360.android.sdk.v2.push.D360GcmListenerService" android:exported="false">
       <intent-filter>
               <action android:name="com.google.android.c2dm.intent.RECEIVE" />
               <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
       </intent-filter>
      </service>
      <!-- END: D360 SDK version 2.1.6 and above -->
      <!-- D360 SDK version below 2.1.6 -->
       <receiver>
                <android:name="de.d360.android.sdk.v2.push.D360GcmBroadcastReceiver">
                <android:permission="com.google.android.c2dm.permission.SEND" >
       <intent-filter android:priority="1">
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
       <category android:name="com.example.app" />
       </intent-filter>
 </receiver>
      <service android:name="de.d360.android.sdk.v2.push.D360GcmIntentService" />
 <!-- END: D360 SDK version below 2.1.6 -->
 <!-- END: GCM Push Receiver -->

       <!-- D360RequestService is system Service for creating requests -->
       <service
               android:name="de.d360.android.sdk.v2.net.D360RequestService"
               android:exported="false" />
       <!-- END: D360RequestService -->

       <!-- InstallTracking. This receiver will be dispatched immediately after installation from market (optional) -->
       <receiver
               android:name="de.d360.android.sdk.v2.broadcast.D360InstallReceiver"
               android:exported="true"
               android:enabled="true">
           <intent-filter>
               <action android:name="com.android.vending.INSTALL_REFERRER" />
           </intent-filter>
       </receiver>

       <!-- Update GCM Registration Token when Android OS version will update (optional) -->
       <receiver android:name="de.d360.android.sdk.v2.broadcast.D360BootReceiver">
           <intent-filter>
               <action android:name="android.intent.action.BOOT_COMPLETED" />
           </intent-filter>
       </receiver>

   </application>

 </manifest>


GCM Setup, Overlays and Deeplink Handling
-----------------------------------------
In the Google developer console you need to navigate to “APIs & auth” > “APIs”, find “Google Cloud Messaging for Android” and make sure it is enabled.

360dialog Backend Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^

In next step you have to go to “APIs & auth” > “Credentials”. Under “Public API access” click on “Create new Key” and choose a “Server key” – to complete the server side set up. It ends with an “API-KEY” which you have to copy-paste into the 360dialog platform interface.

Handling Deeplinks (from Overlay and Push Notifications)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
Deeplinks are a way to navigate the customer to a specific View or Section in your Application. This is done by interpreting the payload of a Push and may also trigger other Actions.

Note: If you’re using the configuration: android:launchMode=”singleTop” you must check the Android SDK FAQ  section for further instructions.

In the 360dialog SDK, handling deeplinks is realized by implementing the interface class D360DeeplinkCallbackInterface. This interface has only one method (execute) with a String parameter that your app needs to handle.

**Here is a code example**::

  public class DeeplinkCallback implements D360DeeplinkCallbackInterface {
   private Context context = null;

   public DeeplinkCallback(Context context) {
       this.context = context;
   }

   @Override
   public void execute(String deeplink) {
       String DEEPLINK_SETTINGS = "settingsActivity";
       String DEEPLINK_JOIN = "joinActivity";

       Intent intent;

       if (null != deeplink && deeplink.equals(DEEPLINK_SETTINGS)) {
           // Go to "SettingActivity" Activity
           intent = new Intent(context, SettingsActivity.class);
       } else if(null != deeplink && (deeplink.equals(DEEPLINK_JOIN)) {
           // Go to "JoinActivity" Activity
           intent = new Intent(context, JoinActivity.class);
       } else {
           // Take default action - go to “MainActivity”
           intent = new Intent(context, MainActivity.class);
       }

       intent.addFlags(Intent.FLAG_ACTIVITY_REORDER_TO_FRONT);
       intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
       context.startActivity(intent);
   }
 }


Init sdk in Application subclass with config::

 D360ConfigContext configContext = new D360ConfigContext();
 configContext.setDeeplinkCallback(new DeeplinkCallback(this));
 D360Sdk.init(this, configContext);

The example shows how to handle two different Deeplinks: “settingsActivity” and “joinActivity”. If the Deeplink String matches the settings of the DeeplinkCallback, then the proper activity is displayed instead of the default.

*Note: You should always provide a default Fallback navigation, in case the payload is not formated correctly.*

A DeeplinkCallback is **required** for the 360dialog SDK to work, especially if your activity is launched by an activity different than your home activity. e.g. if you set up a preloader. Otherwise a user may unintentionally quit the app by using the Return Button on his device after having been Deeplinked.

**Setting a custom Push Notification icon**

In order to set a custom Push Notification icon you have to provide the icon images in the resources folder and declare them in the AndroidManifest.xml file:


<meta-data
      android:name="d360_small_notification_icon"
      <!-- Replace "ic_dialog360_icon" with your Push Notification icon file -->
      android:resource="@drawable/ic_dialog360_icon" />


*Note: If you don’t provide this <meta-data> declaration then the default Push Notification icon will be displayed. You need to set different icons for different screen densities (xhdpi, hdpi, mdpi, …) having the same icon name.*

Indirect Push Notification open
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
360dialog supports a feature to detect if the app was opened in a defined time window (30 minutes by default), while there is a Push Notification “waiting” on the device. If the user opens the app in that time window, the Push Notification action is executed as if the user was actually clicking on it. Note: If you open the app from an external URI, the Push Notification action is not executed even if the user opens the app in the time window.

**Using First- Start- Overlays**

There is a Dialog type to display a First- Start- Overlay to new users on their very first app start. After publishing your app version including the 360dialog SDK every user is potentially a new user for 360dialog backend platform. Based on your own tracking information/ user identification, you have the option to suppress this Overlay in order to exclude known users from seeing it. You need to inform the SDK if your app was updated by the user or if it was a new installation. To do this, start the SDK by using:


D360ConfigContext configContext = new D360ConfigContext();
configContext.setCustomerAppUpdated(true);
D360Sdk.init(this, configContext);

If you set the parameter “customerAppIsUpdated” to “true“, then those users will not see the First- Start- Overlay.
How app navigation is preserved after clicking on notification?

As Android guidelines describes two methods of handling app opens on notification click, we choose the “Special Activity” method described in docs: http://developer.android.com/training/notify-user/navigation.html#ExtendedNotification, with some exceptions:

We don’t expect that you will include android:launchMode=”singleTask”, android:taskAffinity=”” and android:excludeFromRecents=”true” configuration in your activity which will be shown to user after clicking notification.
We will dispatch your main app’s entry point (main activity, this one which is set in your AndroidManifest.xml with action “MAIN” and category “LAUNCHER”).
Underneath of main activity, there can be saved navigation stack (if your app can preserve it), but always main activity should be visible after clicking on notification (unless this is a notification with deeplink or URL).

*Note: You need to detect installation type by yourself before initialising SDK. In most cases you can use previously saved values from e.g. UserPreferences, SQLite databases or other available app storages.*

Event tracking and data collection
----------------------------------

The following section shows you how to track user events and collect data for Segmentation.

SDK events
^^^^^^^^^^
The following events and attributes are captured automatically by the 360dialog
SDK, so you should not send your own events for tracking these properties:

- First Used App (Date)
- Last Used App (Date)
- Total Session Count (Integer)
- Push Enabled (Boolean)
- Country (taken from Device Locale)
- Language (taken from Device Locale)
- Device Model
- Device OS Version
- Device Resolution
- Device Wireless Carrier

The above data is based on the following (SDK) events:

.. list-table:: SDK_Events
   :widths: 1 1 1
   :header-rows: 1

   * - *Event Name*
     - *Parameters*
     - *Description*
   * - **app_OpenedFirstTime**
     - | referrer/fingerprint,
       | appInstanceId, deviceID
     - | Generated only on first
       | App start (after initial
       | install) on device
   * - **app_AppOpened**
     - | params connected with
       | opening an app (e.g. deeplink)
       | previousState
       | (background, destroyed)
     - | Sent when App becomes
       | visible to the user
   * - **app_AppClosed**
     - | nextState (background,
       | destroyed)
     - | Sent when app stop being
       | visible to the user (covers
       | both destroying and
       | Pushing to the
       | background)
   * - **app_RegisterRestrictedData**
     - | name, value
     - | Registration of restricted
       | data, which due to privacy
       | regulations can’t be
       | stored in CRM dbase. One
       | of such examples is
       | google AdvertismentId,
       | which can’t be stored
       | with personal data
   * - **ntf_NotificationClicked**
     - | notificationId
     - | Called from app after
       | opening
   * - **psh_PushReceived**
     - |
     - | Fires when Push message
       | was received by the
       | device
   * - **ntf_AppOpenedByNotification**
     - | cmpId, cmpStepId, pushId,
       | pushVariantId,
       | openingType (direct,
       | indirect)
     - | Sent if the App was
       | opened from a Push
       | Notification
   * - **ovl_OverlayOpened**
     - | cmpId, cmpStepId
     - | Sent when the Overlay
       | is fully loaded and
       | displayed to the user
   * - **ovl_OverlayClicked**
     - | cmpId, cmpStepId
     - | Sent when a link inside
       | the Overlay has been
       | clicked
   * - **ovl_OverlayClosed**
     - | cmpId, cmpStepId
     - | Sent when user
       | closes the Overlay
   * - **sdk_SdkSessionStart**
     - | sessionCounter
     - |
   * - **sdk_SdkSessionEnd**
     - | sessionCounter,
       | sessionLength
     - |
   * - **sdk_SdkDoNotTrack**
     - | requestedState,
       | currentState
     - | Disables tracking in the
       | platform for the user
       | who requested it.
       | Implementation could be
       | customer specific
   * - **test_SubscribeToTestGroup**
     - | groupName
     - | Test group is visible as
       | a segment in CRM

Custom events
^^^^^^^^^^^^^
Custom events give you the flexibility to send data which is tailored to your app’s needs which will then be used as a base for Dialog campaigns. When creating your own events, please make sure to follow the nomenclature that is provided below.

*Note: The events are prefixed with „ev_“ automatically, so you only need to send the event name. All paramters are optional, but you should at least send a parameter „name“ with the event name.*

**IMPORTANT:** The event names **must** be written in CamelCase and always start with a capital letter (e.g “MyEvent”)

.. list-table:: Custom Events
   :widths: 1 1 1
   :header-rows: 1

   * - *Full Event Name*
     - *Parameters**
     - *Description*
   * - **ev_TrackUserId**
     - | useriD
     - | Invoke this event when “your” userId is
       | created, e.g. if the App starts the first
       | time, or when the userId changes for the
       | same Person/Device
   * - **ev_TrackPurchase**
     - | price(currency)
       | (product)
       | (product_group)
       | (service)
     - | To track purchases send this event
       | containing the price and optional other
       | parameters, such as product or group.
       | The “service” means the payment
       | service, such as Apple, Google or Paypal
   * - **ev_TrackLogin**
     - | userId
     - | Track when a User logs into service/App
   * - **ev_TrackSearch**
     - | keyword
     - | Track usage of search function
   * - **ev_TrackAddToCart**
     - | items
     - | Track AddToCart function
   * - **ev_TrackSignup**
     - | userId
     - | Track when a new User signs up with your
       | service/App
   * - **ev_TrackNewsletterSignup**
     - | email
     - | Track subscription to a newsletter
   * - **ev_TrackView**
     - | viewName,
       | (payload)
     - | Invoke as soon as a User opens a specific
       | view
   * - **ev_TrackEvent**
     - | eventName,
       | (payload)
     - | Track any other event not listed above

Tracking Custom Events in Android
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The Android SDK class has special method for Custom Events. The first parameter is the Full Event Name, followed by JSONObject parameters.
You need to import org.json.JSONObject and org.json.JSONException in your activity.

An example for a Custom Event when a user purchases an item could be: “ev_TrackPurchase” with the parameters: price=2.99, currency=EUR, product=Foo. The code looks like this::

 JSONObject eventParameters = new JSONObject();
  try {
    eventParameters.put("name", "PurchaseObject");
    eventParameters.put("price", "2.99");
    eventParameters.put("currency", "EUR");
    eventParameters.put("product", "Foo");
    } catch (JSONException e) {
       Log.d("TAG", "There was JSON Exception...");
    }
  boolean isAutoTriggeredEvent = false;
  D360Sdk.trackEventWithParameters("TrackPurchase", eventParameters, isAutoTriggeredEvent);

As you saw in this example, the parameter “isAutoTriggered” it has to be set it up as FALSE in most of user cases: it means that event you are sending is triggered as result of user interaction with your app. If you have some events you want to track, that are not triggered directly by user interaction, then you can set this flag to TRUE (example: application was updated, there was updated content downloaded, app was used 100 times etc).

*Note: Because the event name is generic (e.g. TrackView), you should always provide a specific name for the actions (e.g.PurchaseObject)*

**Tracking User data**

360dialog provides methods for assigning attributes to a user. These attributes are essential for creating User Segments which will be involved in Dialog campaigns.

**Standard User data**

If you need to provide some information about your user to 360dialog backend, you can do this by updating Person CRM object:

.. list-table:: CustomEvents
   :widths: 1 1 1
   :header-rows: 1

   * - *Data field name*
     - *Value type**
     - *Description*
   * - **customId**
     - | customId
     - | Custom id which can be set by customer
   * - **sn**
     - | String
     - | Surname for the personExample: Schmidt
   * - **gn**
     - | String
     - | Given name for the person.The givenName attribute is
       | used to hold the part of a person’s name which is not
       | their surname nor middle name.Example: James
   * - **cn**
     - | String
     - | Common name for the person.This is the X.500
       | commonName attribute, which contains a name of an
       | object. If the object corresponds to a person, it is
       | typically the person’s full name.
       | All variants of the name that we might want to search on.
       | Practically, forename1 plus surname, and if it’s different
       | known as plus surname.If known is the same as surname
       | then instead of known-as-plus-surname the value will just
       | be the known-as. Where there are non-ASCII characters
       | present, there will also be hidden values with these
       | converted to plain values, allowing diacritic-insensitive
       | searching. This applies to almost all the name attributes.
       | Additionally, where the surname contains spaces, hidden
       | values will be present with the initial part(s) of the
       | surname omitted.Example: James Schmidt
   * - **mail**
     - | String
     - | Person email.Using mailbox format as defined in
       | RFC822.Example: james.schmidt@360dialog.de
   * - **preferredLanguage**
     - | String
     - | Person preferred language. Used to indicate an
       | individual’s preferred written or spoken language.
       | This is useful for international correspondence or
       | human-computer interaction. Values for this attribute
       | type MUST conform to the definition of the Accept-
       | Language header field defined in [RFC2068] with one
       | exception:
       | the sequence “Accept-Language” “:” should be omitted.
       | This is a single valued attribute type.Example: en
   * - **dateOfBirth**
     - | String
     - | Date of birth.Time will always be in ISO8601 date format
       | (YYYY-MM-DD).Example: 2014-03-29T02:11:09.045Z
   * - **sex**
     - | String
     - | Sex of the person.Example: male, female


Updating CRM Person object with Android SDK
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
To update CRM data, prepare JSONObject containing key-values pairs according to table above. Example::

 JSONObject data = new JSONObject();
 try {
   data.put("customId", "xyz987");
   data.put("dateOfBirth", "1990-01-01T00:00:00Z");
   data.put("mail", "customer@mail-provider.com");
 } catch (JSONException e) {
   Log.d("TAG", "JSON Exception caught!");
 }
 D360Sdk.updateCrmData("person", data);

Update CRM objects (Device, AppInstance, Person) (since SDK 2.1.3build1):
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To simplify updating CRM objects, there are simple updaters objects provided now by 360 SDK. To get updater for AppInstance, Device or Person, you need to call respectively::

     D360Sdk.getCrmAppInstanceUpdater();

     D360Sdk.getCrmDeviceUpdater();

     D360Sdk.getCrmPersonUpdater();

Each of this method will return updater object with setters to field available to updating. In case of AppInstance and Device, those fields are “customId”, which you will not need unless you will integrate your custom backend with our CRM via API – then “customId” field can be a key to map CRM objects onto your backend objects.

In case of Person object you have much more setters available, so you can set all data listed on table above.

Each updater has sendUpdateEvent method which is collecting data you set, and sending them to CRM.

Sample updates::

    AppInstanceUpdater appInstance = D360Sdk.getCrmAppInstanceUpdater();

    appInstance.setCustomId(“some123CustomId”);

    appInstance.sendUpdateEvent();

    DeviceUpdater device = D360Sdk.getCrmDeviceUpdater();

    device.setCustomId(“Some321CustomId”);

    device.sendUpdateEvent();

    PersonUpdater person = D360Sdk.getCrmPersonUpdater();

    person.setCustomId(“personCustom123Id”);

    person.setSurname(“Doe”);

    person.setGivenName(“John”);

    person.setDateOfBirth(“2014-03-29T02:11:09.045Z");

    person.sendUpdateEvent();

Currently the old update method is still available, but as it works in different way than the new one, please consult with 360 Dialog which one you need to use in terms of your use case.

Disabling custom events tracking

You can also disable Custom events tracking::

    D360Sdk.disableSdkTracking(true);

To enable tracking Custom events again::

    D360Sdk.disableSdkTracking(false);

Disabling Custom events will still allow the SDK to send Technical events, that don’t track user behavioral related events. Those are:

- psh_PushReceived
- sdk_Pong
- dbg_DuplicatedPushReceived
- sdk_SdkDoNotTrack
- sdk_SessionStart
- sdk_SessionEnd
- sdk_PushTokenRegistration

*Note: In order to create Segments excluding users who opted out from tracking user event data, you need to add the Segment- Tag “Tracking OptIn: no” to your Segment (-definition).*

Installation tracking
---------------------

The Google Play Store features a “referrer” Parameter, that allows you to track the installation of your app. By calling a BroadcastReceiver upon a user opening the app, you can identify the installation source.

https://play.google.com/store/apps/details?id=com.example.myapp&referrer=MyReferrer

Using the Install Tracking
^^^^^^^^^^^^^^^^^^^^^^^^^^
There are two methods to track installations with the 360dialog Android SDK depending on your app using this feature already or not.

**1. The application already tracks installations**

In case you are already using an Install Receiver, you need to modify your BroadcastReceiver to make it  share the Intent with all Receivers.

Note: Although the Android OS allows the declaration of multiple Install Receivers, only the first Receiver will be called, if you don’t extend the class as described!::

 import android.content.BroadcastReceiver;
 import android.content.Context;
 import android.content.Intent;

 import de.d360.android.sdk.v2.broadcast.D360InstallReceiver;
 import com.google.analytics.tracking.android.CampaignTrackingReceiver;

 public class CustomReceiver extends BroadcastReceiver {

 @Override
     public void onReceive(Context context, Intent intent) {
     // Run your receivers
     new D360InstallReceiver().onReceive(context, intent);
     new CampaignTrackingReceiver().onReceive(context, intent);
     }
    }

This CustomReceiver class also needs to be declared in the AndroidManifest.xml file.

*Note: You have to replace “com.example.myapp.CustomReceiver” with your actual class name.*::

 <manifest>
    <application>
       <receiver
       android:name="com.example.myapp.CustomReceiver"
       android:exported="true"
       android:enabled="true">
          <intent-filter>
             <action android:name="com.android.vending.INSTALL_REFERRER" />
          </intent-filter>
       </receiver>
    </application>
 </manifest>

**2. The application does not track installations**

If you are using 360dialog Install Tracking exclusively, you just need to include this code in the AndroidManifest.xml file::

 <manifest>
  <application>
    <receiver
    android:name="de.d360.android.sdk.v2.broadcast.D360InstallReceiver"
    android:exported="true"
    android:enabled="true">
    <intent-filter>
       <action android:name="com.android.vending.INSTALL_REFERRER" />
    </intent-filter>
    </receiver>
  </application>
 </manifest>


Handling UI operations
----------------------

In order to be able to handle operations related to your app’s UI, you need to use another two methods:

``D360Sdk.setLaunchIntent(Intent intent);``

``D360Sdk.dispatchUiOperations();``

The **first one** is important to handle notification-related operations. After user clicks on notification on his device, it is launching app with intent object, containing extra parameters placed by SDK. Those parameters are for attribution app launch to notification click event. If you will not provide launch intent to SDK, it will loose data required to measure campaigns efficiency.

**Second method** is for dispatching any SDK operations which has impact on user interface. Currently these are overlays and deeplinks. This method should be placed somewhere, when it can be run when app is starting. This method is especially important if you want to integrate SDK in OpenGL app.
For example when app is in background and user is bringing app back to foreground, then app have to load all textures again into memory – you can first load textures, then dispatch UI operations.

Basic example of using both methods::

 import de.d360.android.sdk.v2.D360Sdk;

 public class AllMyActivities extends Activity {

   @Override
   protected void onCreate(Bundle savedInstanceState) {
     // … your init code

     D360Sdk.setLaunchIntent(getIntent());
   }

   @Override
   protected void onResume() {
     super.onResume();
     D360Sdk.onResume(this);
     D360Sdk.dispatchUiOperations();
   }

   @Override
   protected void onStop() {
	super.onStop();
   	D360Sdk.onStop(this);
   }

   @Override
   public void onWindowFocusChanged(boolean hasFocus) {
   	D360Sdk.onWindowFocusChanged(this, hasFocus);
   }

   /**
    * This is mandatory especially if your main activity
    * is launched with launchType=singleTop
    * or you are launching your main activity using
    * ACTIVITY_FLAG_SINGLE_TOP
    */
   @Override
   protected void onNewIntent(Intent intent) {
   	super.onNewIntent(intent);
   	D360Sdk.onNewIntent(this, intent);
   }

 }

*Important: Both methods setLaunchIntent and dispatchUiOperations should be placed in any possible app’s entry points! Methods are developed to run once per app business lifecycle (from app becomes visible to app becomes invisible to user). You can safely use them in all of your activities to be completely sure, that there will be no broken use case here.*

Android SDK FAQ and Integration troubleshooting
-----------------------------------------------

**Question:**

I don’t receive Push Notifications for my Application on my device.

**Answer:**

Check if you provided the correct GCM Auth Key in the 360dialog Platform
If you don’t allow sending from all IP- Addresses, make sure that 360dialog are whitelisted.

IPs:
54.72.183.234

54.72.46.201

54.229.99.243

54.229.52.253

54.229.205.247

54.229.99.83

54.72.197.136

54.72.61.104

**Question:**

The application crashes for example if it receives a Push Notification or when displaying an Overlay.

**Answer:**

Make sure you implemented all required permissions in the AndroidManifest.xml and check if you implemented Deeplink handling.

**Question:**

There are no events visible in the event- viewer in the 360dialog Platform when I start the Application.

**Answer:**

Make sure you configured the correct 360 API- Key. If you are using ProGuard you need to add the configuration:

- keep class ``de.d360.android.sdk.v2.** { *; }``
- keep class ``com.google.android.gms.common.** { *; }``

**Question:**

My application uses the configuration android:launchMode=“singleTop” to display activities. When I try to deeplink to them via Push Notification action, my application crashes and there are no events sent.

**Answer:**

If an activity is launched “single top” in some cases Android will not launch it with the expected Intent, especially when the activity was running little time before trying to deeplink to it. You need to add an extra life cycle method to every activity, which you potentially want to deeplink to from a Push Notification (including your MainActivity):

``onNewIntent(Intent intent)``

**Question:**

What is the JAVA language version used to build 360 dialog SDK?

**Answer:**

The SDK is build in JAVA 6 to allow to build it with older android build-tools.

SDK Integration troubleshooting
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Make sure you used in your AndroidManifest.xml all permissions listed in integration manual (sample AndroidManifest.xml listed below)
- Make sure you used in your AndroidManifest.xml all receivers listed in integration manual (sample AndroidManifest.xml listed below)
- Make sure you used in your AndroidManifest.xml all services listed in integration manual (sample AndroidManifest.xml listed below)
- You have created and filled up res/values/d360.xml file (sample d360.xml file listed below)
- Each of your Activity class is extended by one of class:

``de.d360.android.sdk.v2.activities.D360Activity``
 – if your Activity class is currently extended by android.app.Activity
``de.d360.android.sdk.v2.activitiesD360ActionBarActivity``
 – if your Activity class is currently extended by android.support.v7.app.ActionBarActivity
``de.d360.android.sdk.v2.activities.D360FragmentActivity``
 – if your Activity class is currently extended by android.support.v4.app.FragmentActivity

- When you are invoking D360Sdk.start(this); make sure that “this” object is subclass of Context object.
- Make sure you have created deeplink callback and used it before invoking D360Sdk.start(this);. To create deeplink callback reffer to sample snippet  “Deeplink Callback class” and use it as follows:

``deeplinkCallback = new DeeplinkCallback(this);``
``D360Sdk.setDeeplinkCallback(deeplinkCallback);``
``D360Sdk.start(this);``

- Full example of use, you can see in “SDK Integration” sample
- One API-KEY = one application. Don’t allow to switch between different 360 dialog API-KEY’s in your application runtime. This will cause many issues with data inconsistency, also may cause SDK to stop working

