# AdMob Mediation Integration Guide

This guide instructs you step-by-step on how to set Cloudmobi live as an Ad Network on the Admob Mediation platform.

Currently we support Rewarded Video, Banner and Interstitial for mediation.

#### Before You Start

- Make sure you have a Cloudmobi SSP account, & created an application and ad slots on the Cloudmobi Platform.
- To serve Cloudmobi's Ads, you must have the [Cloudmobi Basic Android SDK](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK.zip) integrated also.  

#### Adapter-For-Admob [Download](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK_Adapter-For-Admob.zip)

### Step 1: Create Mediation Group

![image](https://user-images.githubusercontent.com/20314643/34598723-6a3249e2-f229-11e7-96b8-0ba05840c957.png)

1.1 Choose the ad format and platform

Currently we support rewarded video and interstitial mediation for Android

![image](https://user-images.githubusercontent.com/20314643/34598771-abf82482-f229-11e7-8f5b-849a295461fa.png)

1.2 Select your targeting location, turn the status on and "add ad units"

![image](https://user-images.githubusercontent.com/20314643/34598876-34953cd0-f22a-11e7-9d76-f2610179dc99.png)



1.3 Select your application and ad units that you plan to integrate with Cloudmobi

![image](https://user-images.githubusercontent.com/20314643/34598969-da664082-f22a-11e7-9441-c6aca7cd93ba.png)



### step 2: Define Custom Events

”eCPM“ determines the order of the ad network to serve ads.

![image](https://user-images.githubusercontent.com/20314643/34599932-fab8e6c8-f22f-11e7-93df-37119420eb58.png)

![image](https://user-images.githubusercontent.com/20314643/34600140-f0e26a74-f230-11e7-9451-baaaf675b2ce.png)



### Step 3: Add Cloudmobi custom event in Admob console

![image](https://user-images.githubusercontent.com/20314643/34600301-c64459c0-f231-11e7-8ab5-67a61423e5ea.png)

(1)Class Names should match the ad formats in Cloudmobi -- for example, if you are integrating rewarded video ads, the full class name should be: 

 ```
 com.cloudtech.mediation.admob.CTRewardedVideoAdapter 
 
 ```

(2)Parameter is the slot ID in the Cloudmobi platform. You can find it in your app list on the Cloudmobi platform


![image](https://user-images.githubusercontent.com/20314643/34601111-7ecc1b88-f235-11e7-90e1-017785793815.png)



### Step 4: Copy the class into your code folder

- Rewarded video :Copy `CTRewardedVideoAdapter.java` into your code folder
- Interstitial :Copy `CTCustomEventInterstitial.java` into your code folder
- Banner :Copy `CTCustomEventBanner.java` into your code folder

### Step 5: Update the AndroidMenifest.xml for Cloudmobi 

- Rewarded Video

```xml
<!-- for cloudmobi rewarded video -->
<activity android:name="com.cloudtech.videoads.view.CTInterstitialActivity"  			 android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"/>
```

- Interstitial

```xml
<!-- for cloudmobi Interstitial -->	
<activity
	android:name="com.cloudtech.ads.view.InterstitialActivity" />    
```

### Step 6. Add ProGuard Rules

```
-dontoptimize
-dontpreverify
#for sdk
-dontwarn com.cloudtech.**
-keep public class com.cloudtech.**{*;}
#for gaid
-keep class **.AdvertisingIdClient$** { *; }
```
**Done!** You are now all set to deliver Cloudmobi Ads within your application!

For more details information about Custom Events in Admob Mediation, please click [here](https://developers.google.com/admob/android/custom-events)

