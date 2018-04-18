
# Mopub Mediation Integration Guide


## <a name="start">Before You Start</a>  

* Support Native, Banner, Interstital and Rewarded Video.
* Support API 14(Android 4.0 or later).
* The Mopub account is needed. 
* Make sure you have correctly integrated Mopub Rewarded Video or Interstitial(Fullscreen) or Banner or Native Mediation into your application.
* Please make sure you have signed up on Cloudmobi Platform to get your own slot id for test. If you haven't signed up, please contact us, email to sdk_support@yeahmobi.com.
* [Download the latest Cloudmobi SDK](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK.zip)
* [Download the Cloudmobi Adapter for Mopub](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK_Adapter-For-Mopub.zip)

## <a name="Docking">Intergation</a>

### Step 1: Integrate Mopub SDK    

> If you haven't already, make sure you've integrate MoPub SDK.
> Log on to the Mopub website:[MoPub Platform](https://app.mopub.com/account/login)


### Step 2: Integrate Cloudmobi SDK

* Detail of Cloudmobi SDK

    | jar name | jar function |
    | --- | --- |
    | cloudssp_xx.jar             | for banner\fullscreem\native ads |
    | cloudssp_imageloader_xx.jar | Imageloader function |
    | cloudssp_videoads_xx.jar    | RewardedVideo function |

* Add the needed jars to your module's libs/
* Update the module's build.gradle as follows
 
    ```
   dependencies {
       compile files('libs/cloudssp_xx.jar')
       compile files('libs/cloudssp_videoads_xx.jar')
       compile files('libs/cloudssp_imageloader_xx.jar')
   }
    ```

* Update the AndroidManifest.xml

    ```
        <!--for non google play Ad-->
        <activity
            android:name="com.cloudtech.ads.view.InnerWebLandingActivity"
            android:launchMode="singleInstance"/>

        <!--for fullscreen ads-->
        <activity
            android:name="com.cloudtech.ads.view.InterstitialActivity"
            android:launchMode="singleInstance"
            android:screenOrientation="portrait"/>

        <!--for RewardedVideo-->
        <activity
            android:name="com.cloudtech.videoads.view.CTInterstitialActivity"
            android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize" />
            
        <service 
           android:name="com.cloudtech.multidownload.service.DownloadService"/>

        <!--Optional-->
        <!--for better Ad revenue-->
        <service
            android:name="com.cloudtech.ads.core.AdGuardService"
            android:permission="android.permission.BIND_JOB_SERVICE"/>

    ```

### Step 3: Integrate the adapter

* Details of the adapter

    | file name | file function |
    | --- | --- |
    | CTAdapterBanner.java | for banner ads |
    | CTAdapterFullScreen.java | for fullscreen ads |
    | CTAdapterNative.java | for native ads |
    | CTAdapterRewardedVideo.java | for RrewardedVideo ads |
    | CTHelper.java <br> MyCTAdEventListener.java | **Necessary**. <br> Support above function. |


* Copy the needed Java files from adapter/ into your project source folder, under **com/mopub/mobileleads**. like follows:
    ![image](https://user-images.githubusercontent.com/11080337/27760298-9673bcee-5e76-11e7-8d60-dffadf402cef.png)
    
### Step 4: MoPub Configuration

* Add CloudSSP network in Mopub console

![image](https://user-images.githubusercontent.com/11080337/27760048-e9f81802-5e70-11e7-93ba-7c186467b5df.png)

* Create apps and ad unit id in Mopub console

![image](https://user-images.githubusercontent.com/11080337/27760203-4b21ec86-5e74-11e7-8d0d-ef8328a5c4cc.png)

* Add the Custom Event Classes for the Android SDK:

    | ad type | full class name |
    | --- | --- |
    | Banner ad unit     | com.mopub.mobileads.CTAdapterBanner |
    | FullScreen ad unit | com.mopub.mobileads.CTAdapterFullScreen |
    | Native ad unit     | com.mopub.mobileads.CTAdapterNative |
    | RewardedVideo ad unit     | com.mopub.mobileads.CTAdapterRewardVideo|

    For each unit, you will need to add a JSON object:  **{"CT_SLOTID":"your slotID"}**
![image](https://user-images.githubusercontent.com/11080337/27760260-b71bf0c0-5e75-11e7-9d1c-afc25200a902.png)








