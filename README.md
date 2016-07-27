# About
This library provides deep unique analytics bsaed on the financial data of users for any android app. Please check out bi.beewise.in for any queries or reach out to me @ +91 81470 19806.

# Android SDK integration

## Installation in Android Studio

Add dependencies to *app/build.gradle*:
```sh
compile 'com.android.beewisesdk:beewisesdk:1.1.3'
```
Check out https://bintray.com/sidsverma/maven/beewise-sdk for the latest version of the sdk.

## Installation in Eclipse

To integrate our SDK in eclipse, these are the steps:

1. Add following permissions in *app/src/main/AndroidManifest.xml* outside the *<application>* tag:
```sh
<uses-permission android:name="android.permission.READ_SMS"/>
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<uses-feature android:name="android.hardware.location" android:required="true" />
<uses-feature android:name="android.hardware.location.gps" android:required="false" />
```

2. Add following lines in *app/src/main/AndroidManifest.xml* inside the *<application>* tag:
```sh
<receiver
    android:name="com.android.beewisesdk.Receivers.SmsReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="999">
        <action android:name="android.provider.Telephony.SMS_RECEIVED"/>
    </intent-filter>
</receiver>
<receiver
    android:name="com.android.beewisesdk.Receivers.SyncAlarmReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="999">
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>
<receiver
    android:name="com.android.beewisesdk.Receivers.NetworkChangeReceiver"
    android:exported="false"
    android:label="NetworkChangeReceiver">
    <intent-filter>
        <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
    </intent-filter>
</receiver>

<service
    android:name="com.android.beewisesdk.Services.SyncService"
    android:exported="false"/>
<service
    android:name="com.android.beewisesdk.Services.GeoLocationService"
    android:exported="false"/>

<service android:name="com.littlefluffytoys.littlefluffylocationlibrary.LocationBroadcastService" />
<receiver android:name="com.littlefluffytoys.littlefluffylocationlibrary.StartupBroadcastReceiver" android:exported="true">
    <intent-filter> <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
<receiver android:name="com.littlefluffytoys.littlefluffylocationlibrary.PassiveLocationChangedReceiver" android:exported="true" />
<meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version"/>
```
4. Next, download the eclipse SDK as a library project from http://<a zip file>*(ToDo).

5. Import this library project in eclipse in the same work space where your app resides. Then add beewisesdk project as a dependency in your project.

## ProGuard Requirements
In case you use ProGuard, add the following line in your proguard configuration(typically in *proguard-rules.pro*):
```sh
-dontwarn android.test.**
```

## JUnit Issue
In case you face conflict issues around junit and hamcrest, check this out:

http://saltnlight5.blogspot.in/2012/10/whats-up-with-junit-and-hamcrest.html

Work around: Changing it the following way works:
Changing ```compile 'com.android.beewisesdk:beewisesdk:xxx'``` to
```sh
compile ('com.android.beewisesdk:beewisesdk:xxx') {
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
bw.onStart( <your appId> + "" );
```

**To obtain your app id, contact BeeWise at http://bi.beewise.in/**

Do shout out @sidsverma on twitter for any further queries or enhancements! You can also submit an "issue" here on github.
