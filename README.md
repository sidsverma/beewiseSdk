# About

# Android SDK integration

## Installation in Android Studio

Add dependencies to *app/build.gradle*:
```sh
compile 'com.sdk.beewise:beewisesdk:2.0'
```
Check out https://bintray.com/sidsverma/maven/sdk-beewise for the latest version of the sdk.

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
xxx = the version of the sdk you are using.

## GSON Issue
Work around: Exclude the GSON group like this(just below the above statement):
```sh
   exclude group: 'com.google.code.gson'
```
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
In case you want a callback once the task of the onStart function has completed, call this instead:
```sh
bw = BW.getInstance(getApplicationContext());
bw.onStart( <your appId>, new OnTaskCompleted() {
     @Override
     public void onTaskCompleted(Double Result) {
     // Result == 1.0 -> means it synced successfully.
     }
   } );
```
Set the base URL as your server's URL which will contain the various web service calls(This is a mandatory function call):
```sh
bw.setBaseUrl(""); // this has to be your server IP eg. "https://192.168.2.34"
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
Set the default time interval before which the SMSes shouldn't sync again upon calling of bw.onStart():
```sh
bw.setDefaultAppOpenSyncInterval( long <interval in hours> );
```
Set the default time interval before which the SMSes shouldn't sync again upon receiving a new SMS:
```sh
bw.setDefaultNewMessageSyncInterval( long <interval in hours> );
```
Set the default time interval before which the SMSes shouldn't sync again upon calling of bw.nudge():
```sh
bw.setDefaultNudgeSyncInterval( long <interval in hours> );
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
* android.permission.READ_PHONE_STATE(PROTECTION_EXPLICIT**) - This is for dual sim phones to figure out which SIM the SMS transaction was received on.

*PROTECTION_NORMAL signifies: Just simply declare these permissions in AndroidManifest.xml and it will work just fine. No need to check run-time for the permissions listed above since they couldn't be revoked by the user.

**PROTECTION_EXPLICIT signifies: If an app declares that it needs an explicit permission, the user has to explicitly grant the permission to the app.

**To obtain your app id, contact BeeWise at http://bi.beewise.in/**

Do shout out @sidsverma on twitter for any further queries or enhancements! You can also submit an "issue" here on github.

* ensures privacy of the user - does not read any OTP or other such sensitive info. Also, multi-layered RSA encryption is used to ensure absolute privacy here.
