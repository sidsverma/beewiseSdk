# Android SDK integration

## Installation in Android Studio

1. Add dependencies to *app/build.gradle*:
```sh
compile 'com.android.beewisesdk:beewisesdk:1.0'
```

2. If you would like us to track the city of the user, add the following line in *app/src/main/AndroidManifest.xml* outside the<application> tag:
```sh
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
## Installation in Eclipse

To integrate our SDK in eclipse, these are the steps:

1. Add following permissions in *app/src/main/AndroidManifest.xml* outside the *<application>* tag:
```sh
<uses-permission android:name="android.permission.READ_SMS"/>
<uses-permission android:name="android.permission.RECEIVE_SMS"/>
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
```
2. If you would like us to track the city of the user, add the following line in app/src/main/AndroidManifest.xml outside the<application> tag:
```sh
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```
3. Add following lines in *app/src/main/AndroidManifest.xml* inside the *<application>* tag:
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
<receiver android:name="com.android.beewisesdk.Receivers.GpsLocationReceiver">
    <intent-filter>
        <action android:name="android.location.PROVIDERS_CHANGED" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</receiver>

<service
    android:name="com.android.beewisesdk.Services.SyncService"
    android:exported="false"/>
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
```sh
compile ('com.android.beewisesdk:beewisesdk:1.0') {
    transitive=false;
}
```

OR

Changing ```testCompile 'junit:junit:4.12'``` to ```testCompile 'junit:junit:4.10'``` works.

# Using Android SDK

Follow these steps to use our android SDK:

### Import BeeWise SDK in your activity

In all the activity classes, starting with the class for the main activity, import BeeWise SDK:
```sh
import com.android.beewisesdk.BW;
```
### Initialization and cleanup of SDK

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
