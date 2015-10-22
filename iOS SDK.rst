Integration of iOS SDK
======================

Preparation of the project for iOS SDK

iOS SDK Migration 2.0.x - 2.1.x.x
---------------------------------

- There is only one SDK starting method which is: ``[D360SDK startWithApiKey: andConfigContext:]``
- There is ``D360ConfigContext`` object introduced as mandatory param in SDK start method. Read more.
- The ``[D360SDK handlePushNotification:fetchCompletionHandler:]`` method now is returning BOOL value, which says if there was fetch completion handler invoked by SDK or not. Read more.
- There are two delegates to use by developer to interact with sdk: ``D360SDKDeeplinkDelegate`` and ``D360OverlayDelegate``
- There are CRM Objects updaters introduced. Read more.
- There is ``dispatchUiOperations`` method introduced, to define point of time in application, when there can be displayed campaign which will interact with app’s view. Read more.

:doc:`Android reference </Android SDK>`


360dialog Libraries
-------------------

Before going to integration process please be sure that every point of list below is checked on your side:

Agree all the Custom Events that you want track with 360dialog contact person.
You have at least a draft of how campaigns should looks like.

In Apple’s Developer Center (https://developer.apple.com/account/ios/identifiers/bundle/bundleList.action), pick your App ID or create new one if you are just starting your app from scratch. Edit App ID and make sure, that “Push Notifications” entitlement is enabled for your app. Also make sure you have at least “Production SSL Certificate” generated for your app. There may be useful to generate also “Development SSL Certificate”, but not necessary.
Make sure, than at least “Production SSL Certificate” for your app is present in your Keychain on your Mac.

If you want to use also “Development SSL Certificate” for Push notifications, make sure, that all of your test devices are listed in Device List (https://developer.apple.com/account/ios/device/deviceList.action)

Make sure, you have synchronized your Xcode’s certificates with Apple Developer Center, when you have generated certificates, and Push Notifications entitlement is turned ON in App ID. To synchronize certificates, just go to Xcode’s preferences > Accounts > View Details > (Bottom left there is “refresh” button – just click it).

Make sure, you have “Remote notifications” enabled in App Capabilities -> Background modes in your Xcode project.
Be sure, that you have created 360 dialog account, you have login, password and you are able to access Administration > App, so you are able to see your API-KEY to use in your app.

**One API-KEY = one application. Don’t allow to switch between different 360dialog API-KEYs in your application runtime. This will cause many issues with data inconsistency, also may cause SDK to stop working**

If any of step above is not completed on your side, try to accomplish it before starting the integration.

In order to use the iOS SDK you need to compile your project including the following libraries:

``Foundation.framework``
``UIKit.framework``
``CoreTelephony.framework``
``SystemConfiguration.framework``
``libsqlite3.dylib``

Before coding
-------------
Before coding it´s important to be sure that your app is able to handle push communication channel. To do so, you need to login to Apple Developer Center and then:

- Go to: *Identifiers > App IDs > Your app*

- Click *“Edit”* and select *“Push Notifications”*.

Now you can create Push Notification certificate. You have to create at least “Production SSL Certificate” which will be mandatory to receive push messages on your production app. You can create “Development SSL Certificate” as well, which enables you with debugging push messages possibility.
After creating certificate(s), download it, then double click it to get it in your Keychain.


Uploading Push Certificates into 360dialog console
--------------------------------------------------

Prerequisites: you have push certificate(s) loaded into your **KeyChain locally**.

In your KeyChain Access, you can see push certificates by choosing “login” Keychains, and category “Certificates”. Each push services certificate is in fact a pair of Certificate and corresponding Private Key.
Now expand that you want to export by clicking triangle icon on left side of row representing your certificate.
Then, right click on certificate, choose “Export…” and “Save as” *.cer file (example: certificate.cer).
Now right click on private key, choose “Export…” and save as *.p12 format (example: key.p12). If you choose to save with password, and do you prefer to have one for key, then you will be asked for it.
Having exported certificate and key from Keychain, open Terminal app (or whatever app you use to have shell access), and type following commands::

    openssl x509 -in certificate.cer -inform der -out certificate.pem
    openssl pkcs12 -nocerts -out key.pem -in key.p12
    openssl pkcs12 -export -out cert_for_upload.p12 -inkey key.pem -in certificate.pem

*Note: All commands assumes that you named your files respectively: certificate.cer and key.p12. As a result of commands above you will obtain cert_for_upload.p12 file which will be the file you will need to upload on our CRM console. It is also good practice to have password setup for this file, and we are strongly encourage you to have one.*

To upload Certificate (to enable push communication channel for your app), login into 360dialog console and go to: Administration > App > (optional) choose app you want to upload certificate for > click “Edit”.
Within technical app (iPhone/iPad) you can upload the Certificate. If Certificate is in p12 format, it is expected to have password, so there will another password field appear in which you will need to provide *.p12 password. It will be used one time, only to extract Certificate contents, and will be not stored anywhere.
If you are uploading certificate for the first time, it will be working right after upload. If you are updating already existing certificate, it may take up to 1 hour for certificate to be used by our system to send push messages.


Always use different app and api key pair for production and development environment

API-KEY is used by 360 SDK to communicate with service in context of mobile app. This one you can get from UI console in Business app section next to specific platform.

In SDK configuration you are always using customerApp level key. This API-KEY is connected with corresponding APNS certificate or GCM push key (on Android case). This is why You should always use different customer app definition and api-key for each app and different api-key for production and developer environment.

The best way would be to create different  Business app for development and production.

As we can read in APNS documentation:

.. epigraph::

    *You must get separate certificates for the Development environment and the Production environment.*
    *The certificates are associated with an identifier of the app that is the recipient of remote notifications; this identifier includes the App’s bundle ID. When you create a provisioning profile for one of the environments, the requisite entitlements are automatically added to the profile, including the entitlement specific to remote notifications, <aps-environment>.*

    *The two provisioning profiles are called Development and Distribution. The Distribution provisioning profile is a requirement for submitting your app to the App Store.*

Why there should be only one push provider per certificate
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Each provider has to support APN feedback service. After each sending you should collect information about invalid tokens and remove them from system. otherwise it may lead to poor performance in sending.

The Feedback Service
^^^^^^^^^^^^^^^^^^^^

The Apple Push Notification Service (APNS) includes a feedback service to give you information about failed remote notifications. When a remote notification cannot be delivered because the intended app does not exist on the device, the feedback service adds that device’s token to its list. Remote notifications that expire before being delivered are not considered a failed delivery and don’t impact the feedback service. By using this information to stop sending remote notifications that will fail to be delivered, you reduce unnecessary message overhead and improve overall system performance.

Query the feedback service daily to get the list of device tokens. Use the timestamp to verify that the device tokens haven’t been reregistered since the feedback entry was generated. For each device that has not been reregistered, stop sending notifications. APNs monitors providers for their diligence in checking the feedback service and refraining from sending remote notifications to nonexistent apps on devices.

Project preparation
-------------------

Before starting code implementation, please make sure you have enabled “Remote notifications” capability in your Xcode project.

You can find this setting by clicking in left Xcode pane (files structure) on Project, then click on App target, choose “Capabilities, expand “Background modes” on list and make sure “Remote notifications” option is selected.

Implementation
--------------
To install 360SDK in your project, just drag and drop folder with SDK files into your project tree in XCode, just like you do with all other libs. Once XCode will index SDK files, you can start.

**First of all your AppDelegate class has to include**::

    @implementation AppDelegate

    -   (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
      *Register for push messages - NOTE: This is example method, not part of SDK*
      *When to call this method, and it’s implementation is up to you*
      [self registerForRemoteNotificationsWithApplication:application];

      *ID for Advertisement, if needed in your implementation*
      NSString *IDfA = [[[ASIdentifierManager sharedManager] advertisingIdentifier] UUIDString];

      *Build Config Context object*
      D360ConfigContext *d360Config = [[D360ConfigContext alloc] init];
      [d360Config setLogLevel:D360LogLevelAll];
      [d360Config setRequestForTestId:YES];
      [d360Config setCustomerAppUpdated:NO];
      [d360Config setIdForAdvertisement:IDfA];

      *Set custom delegates*
      [D360SDK setOverlayDelegate:[[MyAppCustomOverlayDelegate alloc] init]];
      [D360SDK setDeeplinkDelegate:[[MyAppCustomDeeplinkDelegate alloc] init]];

      *Init Dialog 360 SDK for production/development*
      [D360SDK startWithApiKey:@"PUT_HERE_API-KEY" andConfigContext:d360Config];

      return YES;
    }

    - (void)applicationDidBecomeActive:(UIApplication *)application
    {
      *Allow to display fullscreen overlays (if there are any) from this point of time*
      [D360SDK dispatchUiOperations];
    }

    *Send APNs token to 360 dialog backend*
    -                              (void)application:(UIApplication *)application
    didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
      [D360SDK addDeviceToken:deviceToken];
    }

    *Handle push messages*
    -          (void)application:(UIApplication *)application
    didReceiveRemoteNotification:(NSDictionary *)userInfo
          fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
      *If push message will not be handled by 360 Dialog SDK, just call completion handler*
      if (![D360SDK handlePushNotification:userInfo fetchCompletionHandler:completionHandler]) {
        completionHandler(UIBackgroundFetchResultNoData);
      }
    }

    @end

This setup is almost ready-to go with basic, but powerful CRM functionalities. It will allow you to register device in CRM, track app open/close events, register device to receive push messages and displaying full screen campaigns.

The most important parts to start SDK are:

- Properly set ``D360ConfigContext`` object
- Pass it to ``[D360SDK startWithApiKey: andConfigContext:]``
- In next section  you will find “How to setup config context”.

Setting D360ConfigContext object
--------------------------------

The D360ConfigContext object is intended to be simple settings object to pass before SDK init. Setting fields in D360ConfigContext is optional. You always have to + pass D360ConfigContext instance to SDK when it starts, but there may be no setting set in this object.

You can set the following fields:

logLevel (setLogLevel) – use one of D360LogLevel enum values to set required log level. It will have impact on log verbosity in development version of the app.
Request for test id (setRequestForTestId) - general use of test ID is for internal testing. If you need to have shorter than AppInstanceId identifier of installed app for your testers, you can use 4-letter testId. Just pass “YES” to obtain testId on CRM registration. Not allowed for production apps!
Customer app updated (setCustomerAppUpdated) - Each time SDK starts for the first time ever in your app it is treated by 360dialog as a new installation of app/first start, even if your user just updated his app from store and downloaded for the first time version of your app which contains 360dialog SDK. If you want to inform our backend that this situation was not a first start, but just regular app update, you can obtain this information from app before initialising the SDK, then use this method with “YES” value, if app was just updated and “NO” value if this is really first start after fresh install. Based on this flag the CRM system can take some decisions related to first-start campaigns, depending on your use case.
Apple’s ID for Advertisement (setIdForAdvertisement) - If you plan to use Media component of SDK and use provider which requires IDfA, then you have to set it here. Please, contact us for more details in such case

**Other fields you may find in D360ConfigContext class will be documented as soon as the backend will start to use them.**


Push messages handling
----------------------

There are two important methods to handle push messaging in your app, which you can see in sample AppDelegate (link). These are:

``[D360SDK addDeviceToken:deviceToken];``

``[D360SDK handlePushNotification:userInfo fetchCompletionHandler:completionHandler];``

First of them it is used to inform backend about device token received by application.

Second is used in AppDelegate’s method which receives push message from APNs.

It is NOT recommended to use more than one Push channel providers. Nevertheless SDK is prepared for such cases, SDK uses Fetch Completion Handler, which indicates background data fetching state to system. It is important to use this handler, as app may be terminated by system, in case it takes to much time to background processing.

If there will be received push from 360dialog platform, Fetch Completion Handler will be called internally, and “YES” value will be returned by handlePushNotification:fetchCompletionHandler: SDK method.

In case that when push will not be recognised as 360dialog (most probably when you have another Push provider), then there will be “NO” value returned, which means, that invoking Fetch Completion Handler is now up to you, or provider’s implementation.

More than one push provider risk
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- Missing data in APNS Feedback Service: if more then one party queries APNS feedback service, then it will get response data from pushes from both services.
- Race conditions on push delivery if user is offline: If the user is offline (no net connection) and both services are sending pushes, then only one push will be delivered (most probably the last one). This will have impact on both systems quality performance. In some cases this impact may be significant.

Excerpt from APNS documentation::

    “If APNs attempts to deliver a notification but the device is offline, the notification is stored for a limited period of time, and delivered to the device when it becomes available.

Only one recent notification for a particular app is stored. If multiple notifications are sent while the device is offline, each new notification causes the prior notification to be discarded. This behavior of keeping only the newest notification is referred to ascoalescing notifications. If the device remains offline for a long time, any notifications that were being stored for it are discarded.”

Tracking custom events
----------------------
To simplify tracking events, we provide just one method to do so:

``[D360SDK trackEvent:(NSString *)eventName withParameters:(NSDictionary *)parameters];``

Depending on the action that you want to track you can pass an Event name as it follows:

- TrackUserId
- TrackPurchase
- TrackLogin
- TrackSearch
- TrackAddToCart
- TrackSignup
- TrackNewsletterSignup
- TrackView
- TrackEvent

If the action that you want to track didn´t fit with the Events that are mentioned above, please use TrackEvent by default, and use the parameters included to custom it.

As parameters you should create NSDictionary object which in most cases will be 1-level deep, which will be simple key-value pairs of NSString objects. Of course this may differ depending on your use case, and this will be defined before implementation, when events will be agreed with 360 Dialog management.

Samples:

**One level event**::

    NSString *eventName = @"TrackSearch";
    NSDictionary *eventParams = @{
    @"query": @"mobile CRM",
    };

    [D360SDK trackEvent:(NSString *)eventName
    withParameters:(NSDictionary *)eventParams];


**Two level event**::

 NSDictionary *eventParams = @{
        @"userId": @1234,
        @"subscribedTo": @{
            @"news",
            @"politics",
            @"sport"
        }
    };
    [D360SDK trackEvent:(NSString *)eventName
    withParameters:(NSDictionary *)eventParams];

.. epigraph::
    **Important!** Before tracking any events, make sure there are events agreed with 360dialog!

Deeplink Delegate protocol (handling deeplinks)
-----------------------------------------------
Deeplinks are direct links to view in your application. There are two kinds of deeplink handled on 360dialog platform:

Internal deeplink (e.g. “settings”, “imprint”)
External deeplink, which is in fact an URL with custom scheme registered in system (e.g. “myapp://com.mycompany/settings”, “myapp://com.mycompany”)
External deeplinks usually are handled by you using proper method in AppDelegate, nevertheless, you will have to be prepared, that external link will be sent to your app as an action on overlay interaction, so it may be also be passed to SDK to be executed.

As SDK don’t know where to your deeplink should point, it’s up to you to define how to handle deeplinks. You need Deeplink Delegate for this task, which should implement D360SDKDeeplinkDelegate protocol.

D360SDKDeeplinkDelegate protocol is using method named “executeDeeplink” which is receiving deeplink as a NSString object. You need to provide your way of deeplink execution in “executeDeeplink” method. At the end, when Deeplink Delegate class is ready, you have to pass it’s instance to SDK using D360SDK method::

+ (void)setDeeplinkDelegate:(id<D360SDKDeeplinkDelegate>)delegate;

If this protocol will be not implemented, or implemented incorrectly it will result in not working deeplinks.

Overlay Delegate protocol
-------------------------

If you would like to react on user interaction on CRM Overlay, you can do so by using D360SDKOverlayDelegate protocol. It contains two methods::

 -(void)userDidClickOnOverlay

 -(void)userDidDismissOverlay

And they are used respectively when user clicks on Overlay or dismissed (closed) Overlay. Apart from standard events which will be sent automatically by SDK, you can attach your own logic for user interaction on overlay (e.g. send events to 3rd party tracking service).

Update CRM Objects (AppInstance, Person, Device)
------------------------------------------------

There are some use cases, when you will need to map CRM objects to objects that are used in your backend and use shared key for this mapping. Also, you may want to specify some Person data and put them to our Person object. In such case you will need to use CRM Updaters objects.

**In case of AppInstance and Device objects there are currently only “customId” field available to update from SDK level.**

Samples:

**AppInstance updater**::

 D360CrmAppInstanceUpdater *appInstanceUpd = [D360SDK crmAppInstanceUpdater];
 [appInstanceUpd setCustomId:@"my-custom-id"];
 [appInstanceUpd sendUpdateEvent];

**Device Updater**::

 D360CrmDeviceUpdater *deviceUpd = [D360SDK crmDeviceUpdater];
 [deviceUpd setCustomId:@"my-custom-id"];
 [deviceUpd sendUpdateEvent];

In case of **Person** there is much more for you to **update**::

    D360CrmPersonUpdater *personUpd = [D360SDK crmPersonUpdater];
    [personUpd setCustomId:@"my-custom-id"];
    [personUpd setSurname:@"Schmidt"];
    [personUpd setGivenName:@"Hans"];
    [personUpd setCommonName:@"Hans"];
    [personUpd setMail:@"hans@schmidt.de"];
    [personUpd setPreferredLanguage:@"de"];
    [personUpd setDateOfBirth:@"1970-01-01T00:00:00.000Z"];
    [personUpd setSex:@"male"];
    [personUpd sendUpdateEvent];

You always have to invoke sendUpdateEvent in case to trigger update on backend-side.

In current implementation you will not get notified when update takes place, as this is asynchronous system.

Updaters objects have “get” methods for each field, but those methods are never returning current state of backend object. This are getters for values you just set before update.


Obtaining SDK IDs
-----------------

In case you need some of IDs which are hold by SDK, you can access them using public getters in D360SDK class::

 [D360SDK getAppInstanceId];

 [D360SDK getDeviceId];

 [D360SDK getPersonId];

 // Test Id will be returned if exists, if there was request to create one on CRM  registration

 [D360SDK getTestId];

Also SDK version is available to get::

 [D360SDK getVersion];


*emphasis*
**bold**
``backquotes``