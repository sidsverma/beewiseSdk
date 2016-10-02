# About

# Android SDK integration

## Installation in Android Studio

Add dependencies to *app/build.gradle*:
```sh
compile 'com.sdk.beewise:beewisesdk:1.2.16'
```
Check out https://bintray.com/sidsverma/maven/beewise-sdk for the latest version of the sdk.

## Installation in Eclipse

To integrate our SDK in eclipse, these are the steps:

1. Add following permissions in *app/src/main/AndroidManifest.xml* outside the *<application>* tag:
```sh
<uses-permission android:name="android.permission.READ_SMS" />
<uses-permission android:name="android.permission.RECEIVE_SMS" />
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<uses-feature
    android:name="android.hardware.location"
    android:required="true" />
<uses-feature
    android:name="android.hardware.location.gps"
    android:required="false" /> <!-- Permissions required for GCM -->
```

2. Add following lines in *app/src/main/AndroidManifest.xml* inside the *<application>* tag:
```sh
<receiver
    android:name=".Receivers.BeeWiseSdkSmsReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="999">
        <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
    </intent-filter>
</receiver>
<receiver
    android:name=".Receivers.BeeWiseSdkSyncAlarmReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="999">
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
<receiver
    android:name=".Receivers.BeeWiseSdkNetworkChangeReceiver"
    android:exported="false"
    android:label="NetworkChangeReceiver">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
    </intent-filter>
</receiver>
<receiver
    android:name="com.android.beewisesdk.Receivers.BeeWiseSdkOnBootReceiver"
    android:exported="false">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>

<service
    android:name="com.android.beewisesdk.Services.SyncService"
    android:exported="false"/>
<service
    android:name="com.android.beewisesdk.Services.GeoLocationService"
    android:exported="false"/>
<service
    android:name="com.android.beewisesdk.Services.BeeWiseSdkSmsReceivedService"
    android:exported="false"/>

<service android:name="com.littlefluffytoys.littlefluffylocationlibrary.LocationBroadcastService" />
<receiver android:name="com.littlefluffytoys.littlefluffylocationlibrary.StartupBroadcastReceiver" android:exported="true">
    <intent-filter> <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
<receiver android:name="com.littlefluffytoys.littlefluffylocationlibrary.PassiveLocationChangedReceiver" android:exported="true" />
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version"/>
```
4. Next, download the eclipse SDK as a library project from [get in touch with us for this].

5. Import classes.jar in libs/ directory of your project

6. Import classes.jar in your project 

## ProGuard Requirements
In case you use ProGuard, add the following line in your proguard configuration(typically in *proguard-rules.pro*):
```sh
-dontwarn android.test.**
```

## JUnit Issue
In case you face conflict issues around junit and hamcrest, check this out:

http://saltnlight5.blogspot.in/2012/10/whats-up-with-junit-and-hamcrest.html

Work around: Changing it the following way works:
Changing ```compile 'com.sdk.beewise:beewisesdk:xxx'``` to
```sh
compile ('com.sdk.beewise:beewisesdk:xxx') {
    exclude module: 'junit'
}
```
works.
Also, you can exclude any other module here in case you get a 'multiple dex' error.

# Using Android SDK

Follow these steps to use our android SDK:

### Import BeeWise SDK in your activity

In all the activity classes, starting with the class for the main activity, import BeeWise SDK:
```sh
import com.android.beewisesdk.BW;
```
### Initialization of SDK

Define a variable called bw in your activity:
```sh
private BW bw;
```
In the onStart() function of your activity, if you want to start BeeWise's SMS reading functionalities, add the following:
```sh
bw = BW.getInstance(getApplicationContext());
bw.onStart( <your appId> );
```
If you want to assign a unique custom user id to refer to this user in our APIs, add the following before calling ```bw.onStart()```:
```sh
bw.setCustomId( <unique_custom_user_id> );
```
You can also set the emailId of this user to improve readability(this currently cannot be used to refer to the particular user):
```sh
bw.setEmailId( <email_id> );
```
Set the debug mode to true if you want to enable debug logs:
```sh
bw.setDebugMode(true);
```

### Marshmallow permission related changes
Client apps need to take run-time permissions for the SDK to work completely. Although, at no time, will the sdk lead to any crashes in the app, but to ensure proper functioning of the SDK, the client app would need to ask for the following mandatory permissions from the user:
* android.permission.INTERNET(PROTECTION_NORMAL*)
* android.permission.ACCESS_NETWORK_STATE(PROTECTION_NORMAL*)
* android.permission.READ_SMS(PROTECTION_EXPLICIT**)
* android.permission.RECEIVE_SMS(PROTECTION_EXPLICIT**)

Once the app has been explicitly granted the SMS related permissions, the app would need to call the following to kick-start the SMS reading process:
```sh
BW bw = BW.getInstance(getApplicationContext());
bw.nudge( <your appId> );
```
Non-mandatory permissions to seek from the user are:
* android.permission.RECEIVE_BOOT_COMPLETED(PROTECTION_NORMAL*) - This is to call the backend within a period of 24 hours(without fail).
* android.permission.ACCESS_COARSE_LOCATION(PROTECTION_EXPLICIT**) - This is to get the approx location of the user during a transaction.
* android.permission.ACCESS_FINE_LOCATION(PROTECTION_EXPLICIT**) - This is to get the exact location of the user during a transaction.

*PROTECTION_NORMAL signifies: Just simply declare these permissions in AndroidManifest.xml and it will work just fine. No need to check run-time for the permissions listed above since they couldn't be revoked by the user.

**PROTECTION_EXPLICIT signifies: If an app declares that it needs an explicit permission, the user has to explicitly grant the permission to the app.

**To obtain your app id, contact BeeWise at http://bi.beewise.in/**

Do shout out @sidsverma on twitter for any further queries or enhancements! You can also submit an "issue" here on github.

* ensures privacy of the user - does not read any OTP or other such sensitive info. Also, multi-layered RSA encryption is used to ensure absolute privacy here.
