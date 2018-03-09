# Getting Started with the Cloudmobi Android SDK

* [Before you start](#start)
* [step 1. Add the Cloudmobi SDK in your project](step1)
* [step 2. Update AndroidMainifest.xml](step2)
* [step 3. Initialize the Cloudmobi SDK](step3)
* [step 4. Android code obfuscation](step4)
* [Something you should know---Integration Note](note)
* [Native](#native)
    * [Element-Native](#common)
    * [Element-Native with imagePreload](#preload)
    * [Element-Native with adCache](#cache)
    * [Element-Native for Multiple](#multi)
    * [Template-Native](#template)
* [Banner](#banner)
* [Interstitial](#interstitial)
* [Appwall](#appwall)
* [Basic Ad Mediation (Banner-Interstitial-Native)](#basic)
    * [facebook](#facebook)
    * [admob](#admob)
* [Rewarded Video](#reward)
* [Error Code For SDK](#error)

## <a name="start">Before You Start</a>

- Cloudmobi Android SDK supports banner, interstitial, native, and rewarded video 
- Furthermore, Cloudmobi Android SDK supports mediation with facebook and Admob via SDK in banner, interstitial and native ads
- Cloudmobi Android SDK supports Android API 14 or later
- Please make sure you have signed up on the Cloudmobi Platform. If you haven't signed up, please contact us via email: sdk_support@yeahmobi.com
- Please make sure you have added an app and at least one ad slot in Cloudmobi Platform
- Please download [our latest SDK](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK.zip)

## <a name="Add">Step 1. Add the Cloudmobi SDK in your project</a>

We supports both Maven dependencies and jar dependencies to integrate our SDK:

### Option 1. Integrating the Cloudmobi SDK in to your APP manually


* [Download the latest SDK](https://github.com/cloudmobi/AndroidSDK/blob/master/AndroidSDK.zip)
* Detail of the different jars：

  | jar name                    | jar function                             | require(Y/N) |
  | --------------------------- | ---------------------------------------- | ------------ |
  | cloudssp_xx.jar             | basic functions(banner\interstitial\native ads) | Y            |
  | cloudssp_imageloader_xx.jar | imageloader functions                    | N            |
  | cloudssp_appwall_xx.jar     | appwall ads functions                    | N            |
  | cloudssp_videoads_xx.jar    | video ads functions                      | N            |

* Update the module's build.gradle for basic functions：

``` groovy
    dependencies {
        compile files('libs/cloudssp_xx.jar')
    }
```

### Option 2. Integrating the Cloudmobi SDK as an AAR via Maven

* Maven version notes:  the newest version is 2.4.0.
* Detail of the different link

| link name                       | link function         | require(Y/N) |
| ------------------------------- | --------------------- | ------------ |
| com.cloudtech:ads:2.4.0         | basic functions       | Y            |
| com.cloudtech:imageloader:2.4.0 | imageloader functions | N            |
| com.cloudtech:appwall:2.4.0     | appwall ads functions | N            |
| com.cloudtech:videoads:2.4.0    | video ads functions   | N            |

* Update the module's build.gradle for basic functions：

``` groovy
    dependencies {
        compile 'com.cloudtech:ads:2.4.0'
    }
```

## <a name="step2">Step 2. Update AndroidManifest.xml</a>  

``` xml
<!--Necessary Permissions-->
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

<!--Optional Permissions-->
<uses-permission android:name="android.permission.READ_PHONE_STATE"/>


   <!-- Necessary -->
   <activity android:name="com.cloudtech.ads.view.InnerWebLandingActivity"
       android:launchMode="singleInstance"/>

   <service
       android:name="com.cloudtech.ads.core.AdGuardService"
       android:permission="android.permission.BIND_JOB_SERVICE"/>

   <service android:name="com.cloudtech.multidownload.service.DownloadService"/>

```

## <a name="step3">Step 3. Initialize the Cloudmobi SDK</a>  

You can init the SDK in your application as detailed below:

```java
   CTService.init(context, "one of your slotId");
```

## <a name="step4">Step 4. Android code obfuscation</a> 

If you are using ProGuard with the Cloudmobi SDK, you must add the following code to your ProGuard file:

``` java
    #for sdk
    -keep public class com.cloudtech.**{*;}
    -dontwarn com.cloudtech.**

    #for gaid
    -keep class **.AdvertisingIdClient$** { *; }

    #for js and webview interface
    -keepclassmembers class * {
        @android.webkit.JavascriptInterface <methods>;
    }

    #for not group facebook/admob ads
    -dontwarn com.google.android.**
    -dontwarn com.facebook.ads.**
```


## <a name="note">Integration Notes</a>

* If you live in a country, such as China, which is forbidden google play, two prerequisites to get Cloudmobi ads: 

  1. GooglePlay has installed on your device.
  2. Your device have connect to VPN.

* About the CTAdEventListerner.

   We suggest you define a class to implement the CTAdEventListener yourself , then you can just override the methods you need when you getBanner or getNative. See the following example:

``` java
public class MyCTAdEventListener implements CTAdEventListener {

    	@Override
    	public void onAdviewGotAdSucceed(CTNative result) {
        	showMsg("onAdviewGotAdSucceed");
    	}
    	
    	@Override
        public void onAdsVoGotAdSucceed(AdsNativeVO result) {
            showMsg("onAdsVoGotAdSucceed");
        }

    	@Override
    	public void onInterstitialLoadSucceed(CTNative result) {
        	showMsg("onInterstitialLoadSucceed");
    	}

	    @Override
    	public void onAdviewGotAdFail(CTNative result) {
       		showMsg(result.getErrorsMsg());
       		Log.i("sdksample", "errorMsg:" + result.getErrorsMsg());
    	}

	    @Override
    	public void onAdviewIntoLandpage(CTNative result) {
        	showMsg("onAdviewIntoLandpage");
    	}

    	@Override
    	public void onStartLandingPageFail(CTNative result) {
        	showMsg("onStartLandingPageFail");
    	}

    	@Override
    	public void onAdviewDismissedLandpage(CTNative result) {
        	showMsg("onAdviewDismissedLandpage");
    	}

    	@Override
    	public void onAdviewClicked(CTNative result) {
        	showMsg("onAdviewClicked");
    	}

    	@Override
    	public void onAdviewClosed(CTNative result) {
        	showMsg("onAdviewClosed");
    	}

	    @Override
    	public void onAdviewDestroyed(CTNative result) {
        	showMsg("onAdviewDestroyed");
    	}


    	private void showMsg(String msg){
        	Toast.makeText(context, msg, Toast.LENGTH_SHORT).show();
    	}
	}
```

## <a name="native">Native Ads Integration</a>

### <a name="common">Single elements-Native ads interface</a>

* The container and the layout for elements-Native ad:

```java
    ViewGroup container = (ViewGroup) view.findViewById(R.id.container);
    ViewGroup adLayout = (ViewGroup)View.inflate(context,R.layout.native_layout, null);
```

* The method to load elements-Native Ads:

``` java

    /**
     * @param slotId     cloudmobi id
     * @param context    context
     * @param imageType  the rate of image(1.9:1 or 1:1) 
     * @param adListener callback listener 
     * @return 
     */
 	CTService.getAdvanceNative("your slotid",context,
 	          CTImageRatioType.RATIO_19_TO_10,new MyCTAdEventListener(){
                  @Override
                  public void onAdviewGotAdSucceed(CTNative result) {
                      if (result == null){
                          return;
                      }
                      CTAdvanceNative ctAdvanceNative = (CTAdvanceNative) result;
                      showAd(ctAdvanceNative);
                      super.onAdviewGotAdSucceed(result);
                  }
        });
```

* Show the elements-Native ads

``` java
    private void showAd(CTAdvanceNative ctAdvanceNative) {
        ImageView img = (ImageView) adLayout.findViewById(R.id.iv_img);
        ImageView icon = (ImageView) adLayout.findViewById(R.id.iv_icon);
        TextView title = (TextView)adLayout.findViewById(R.id.tv_title);
        TextView desc = (TextView)adLayout.findViewById(R.id.tv_desc);
        Button click = (Button)adLayout.findViewById(R.id.bt_click);
        ImageView adChoice = (ImageView)adLayout.findViewById(R.id.choice_icon);
        
        //show the image and icon yourself 
        String imageUrl = ctAdvanceNative.getImageUrl();
        String iconUrl = ctAdvanceNative.getIconUrl();          
        title.setText(ctAdvanceNative.getTitle());
        desc.setText(ctAdvanceNative.getDesc());
        click.setText(ctAdvanceNative.getButtonStr());
        adChoice.setImageURI(ctAdvanceNative.getAdChoiceIconUrl());

        //Optional 1: add your view into ctAdvanceNative，and add the ctAdvanceNative into your container.
        ctAdvanceNative.addADLayoutToADContainer(adLayout);
        container.addView(ctAdvanceNative);
        
        //Optional 2：just register the click for your view, add your view into your container.
        ctAdvanceNative.registeADClickArea(adLayout);
        container.addView(adLayout); 
   }
   
```

### <a name="preload">Single elements-Native ads interface for imageProload</a>

``` java 
        
    // Update the module's build.gradle
    dependencies {
        compile files('libs/cloudssp_xx.jar')       
        compile files('libs/cloudssp_imageloader_xx.jar')   // for preload image
    }  
    
    or
    
    dependencies {
        compile 'com.cloudtech:ads:2.4.0'
        cimpile 'com.cloudtech:imageloader:2.4.0'   // for preload image
    } 
     

    //load ads
    /**
     * @param slotId        cloudmobi id
     * @param context       context
     * @param imageType     the rate of image(1.9:1 or 1:1)
     * @param autoLoadImage is image preload 
     * @param adListener    callback listener
     * @return
     */
    CTService.getAdvanceNative("your slotid", context,
            CTImageRatioType.RATIO_19_TO_10, true, new MyCTAdEventListener() {
                @Override
                public void onAdviewGotAdSucceed(CTNative result) {
                    if (result == null) {
                        return;
                    }
                    CTAdvanceNative ctAdvanceNative = (CTAdvanceNative) result;
                    showAd(ctAdvanceNative);
                    super.onAdviewGotAdSucceed(result);
                }
            });
            
    //show ads
    private void showAd(CTAdvanceNative ctAdvanceNative) {
        ImageView img = (ImageView) adLayout.findViewById(R.id.iv_img);
        ImageView icon = (ImageView) adLayout.findViewById(R.id.iv_icon);
        TextView title = (TextView)adLayout.findViewById(R.id.tv_title);
        TextView desc = (TextView)adLayout.findViewById(R.id.tv_desc);
        Button click = (Button)adLayout.findViewById(R.id.bt_click);
        ImageView adChoice = (ImageView)adLayout.findViewById(R.id.choice_icon);
        
        //show the preload image and icon through sdk api 
        ctAdvanceNative.setIconImage(icon);
        ctAdvanceNative.setLargeImage(img);
        title.setText(ctAdvanceNative.getTitle());
        desc.setText(ctAdvanceNative.getDesc());
        click.setText(ctAdvanceNative.getButtonStr());
        adChoice.setImageURI(ctAdvanceNative.getAdChoiceIconUrl());

        //Optional 1: 
        ctAdvanceNative.addADLayoutToADContainer(adLayout);
        container.addView(ctAdvanceNative);
        
        //Optional 2：
        ctAdvanceNative.registeADClickArea(adLayout);
        container.addView(adLayout); 
   }
   
```

### <a name="cache">Single elements-Native ads interface for AdCache</a>

* get Ads for cache

```java
        
    /**
     * @param slotId     cloudmobi id
     * @param context    context
     * @param imageType  the rate of image(1.9:1 or 1:1)
     * @param adListener callback listener 
     * @return
     */
    CTService.getAdvanceNativeForCache("your slotid",context,
            CTImageRatioType.RATIO_19_TO_10, new MyCTAdEventListener() {
                @Override
                public void onAdsVoGotAdSucceed(AdsNativeVO result) {
                    if (result == null) {
                        return;
                    }
                    AdsNativeVO adNativeVO = result;
                    super.onAdsVoGotAdSucceed(result);
                }
            });

```

* show ads from cache

```java
      
      CTAdvanceNative ctAdvanceNative = new CTAdvanceNative(getContext());

      AdsNativeVO nativeVO = adNativeVO;

       if (nativeVO != null) {
           ctAdvanceNative.setNativeVO(nativeVO);
    
           ctAdvanceNative.setSecondAdEventListener(new MyCTAdEventListener(){
               @Override
               public void onAdviewClicked(CTNative result) {
                   super.onAdviewClicked(result);
               }
           });
           
           showAd(ctAdvanceNative);
       }

```



### <a name="multi">Multi elements-Native ads interface</a>

* The method to load multi Native ads

``` java

    /**
     * @param reqAdNumber request ads num
     * @param slotId      cloudmobi id
     * @param context     context
     * @param imageType   the rate of image(1.9:1 or 1:1)
     * @param adListener  callback listener 
     * @return
     */
	CTService.getMultiNativeAds(required_num, "your slotid",context,
	   CTImageRatioType.RATIO_19_TO_10,new MultiAdsEventListener() {
            public void onMultiNativeAdsSuccessful(List<CTAdvanceNative> res) {
                //use the List<CTAdvanceNative> in listview or recycleview
            }

            @Override
            public void onAdviewGotAdFail(CTNative result) {

            }
        });
```

### <a name="template">Template-Native ads interface</a>

* The method to load templat-Native Ads:(the template is set up in ssp.)

``` java
	ViewGroup container = (ViewGroup) view.findViewById(R.id.container);

    /**
     * @param slotId            cloudmobi id
     * @param isShowCloseButton close button switch 
     * @param context           context
     * @param adListener        callback listener 
     * @return
     */
 	CTService.getNative("your slotid", false, context,
                new MyCTAdEventListener(){
                    @Override
                    public void onAdviewGotAdSucceed(CTNative result) {
                        if (result != null) {
                            container.removeAllViews();
                            container.addView(result);
                        }
                        super.onAdviewGotAdSucceed(result);
                    }
                });
                
```

## <a name="banner">Banner Ads Integration</a>

* The method to load Banner Ads:

``` java
	ViewGroup container = (ViewGroup) view.findViewById(R.id.container);

    /**
     * @param slotId            cloudmobi id
     * @param isShowCloseButton close button switch 
     * @param context           context
     * @param adListener        callback listener 
     * @return
     */
  	CTService.getBanner("your slotid", false, context,
  	 		new MyCTAdEventListener(){

                    @Override
                    public void onAdviewGotAdSucceed(CTNative result) {
                        if (result != null){
                            container.removeAllViews();
                            container.addView(result);
                        }
                        super.onAdviewGotAdSucceed(result);
                    }

                    @Override
                    public void onAdviewGotAdFail(CTNative result) {
                        super.onAdviewGotAdFail(result);
                    }

                    @Override
                    public void onAdviewClicked(CTNative result) {
                        super.onAdviewClicked(result);
                    }

                    @Override
                    public void onAdviewClosed(CTNative result) {
                        container.removeAllViews();
                        super.onAdviewClosed(result);
                    }
                });
```

## <a name="interstitial">Interstitial Ads Integration</a>

* update AndroidManifest.xml for Interstitial Ads

 ``` xml
        <activity
            android:name="com.cloudtech.ads.view.InterstitialActivity"
            android:launchMode="singleInstance"
            android:screenOrientation="portrait"
            android:multiprocess="true">
        </activity>
        
        the multiprocess attribute is optional.
 ```

* The method to show Interstitial Ads

``` java

    /**
     * @param slotId            cloudmobi id
     * @param isShowCloseButton close button switch 
     * @param isFullScreen      full screen switch 
     * @param context           context
     * @param adListener        callback listener 
     * @return
     */
   CTService.preloadInterstitial("your slotid",true,false,context,
    		  new MyCTAdEventListener(){
                   @Override
                   public void onInterstitialLoadSucceed(CTNative result) {
                       super.onInterstitialLoadSucceed(result);
                   }
    
                   @Override
                   public void onAdviewGotAdSucceed(CTNative result){
                    	if (result != null && result.isLoaded()){
                           CTService.showInterstitial(result);
                       }
                       super.onAdviewGotAdSucceed(result);
                   }
    
                   @Override
                   public void onAdviewClosed(CTNative result) {
                       super.onAdviewClosed(result);
                   }

            });
                
```

## <a name="appwall">Appwall integration</a>

* Update the module's build.gradle for Appwall：

``` groovy
	dependencies {
        compile files('libs/cloudssp_xx.jar')
        compile files('libs/cloudssp_appwall_xx.jar')       // for appwall        
        compile files('libs/cloudssp_imageloader_xx.jar')   // for imageloader
	}
	
	or
	
	dependencies {
        compile 'com.cloudtech:ads:2.4.0'
        compile 'com.cloudtech:appwall:2.4.0'       // for appwall        
        compile 'com.cloudtech:imageloader:2.4.0'   // for imageloader
	}

```

* Update the AndroidManifest.xml for Appwall

``` xml
	 <activity
        android:name="com.cloudtech.appwall.AppwallActivity"
        android:launchMode="singleInstance"
        android:screenOrientation="portrait"
        android:multiprocess="true"/>
        
        the multiprocess attribute is optional.
```

* Preload appwall

    It‘s better to preload ads for Appwall, to ensure they show properly and in a timely fashion. You can have ads preload with the following line of code:


``` java

    AppwallHelper.init(context, "your slotid");
```

* Customize the appwall color theme(optional).

``` java
    CustomizeColor custimozeColor = new CustomizeColor();
    custimozeColor.setMainThemeColor(Color.parseColor("#ff0000ff"));
    AppwallHelper.setThemeColor(custimozeColor);
```

* Show Appwall.

``` java
    AppwallHelper.showAppwall(context, "your slotid");
```

## <a name="basic">Basic Ad Mediation</a> [Banner-Interstitial-Native]

### <a name="facebook">Facebook</a>

* [Facebook advertisement](https://developers.facebook.com/docs/audience-network)
* Set up the facebook placementId in Cloudmobi platform.
* Update the module's build.gradle, as per below:

``` groovy
	dependencies {
    	compile 'com.facebook.android:audience-network-sdk:4.25.0'
	}
```

### <a name="admob">Google Admob</a>

* [Google Admob advertisement](https://firebase.google.com/docs/admob/android/quick-start)
* Set up the admob unitId in Cloudmobi platform.
* Update the module's build.gradle ad bolow:

``` groovy
	dependencies {
    	compile 'com.google.android.gms:play-services-ads:11.8.0'
	}
```

* Update AndroidManifest.xml for admob ads:

```xml
    <meta-data
        android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
    
    <activity 
        android:name="com.google.android.gms.ads.AdActivity"
        android:configChanges="keyboard|keyboardHidden|orientation|
            screenLayout|uiMode|screenSize|smallestScreenSize"
        android:theme="@android:style/Theme.Translucent" />
```

## <a name="reward">Rewarded Video Ad Integration</a>

* **Google Play Services**

    1、Google Advertising ID

    The Rewarded Video function requires access to the Google Advertising ID in order to operate properly.
     See this guide on how to integrate [Google Play Services](https://developers.google.com/android/guides/setup).

    2、Google Play Services in Your Android Manifest

    Add the following  inside the <application> tag in your AndroidManifest:

    ```
    <meta-data android:name="com.google.android.gms.version"
              android:value="@integer/google_play_services_version" />
    ```

    3、If you have integrated the admob-sdk for basic ads, it's not necessary to do this.


* Update the module's build.gradle for Rewarded Video：

``` groovy
	dependencies {
        compile files('libs/cloudssp_xx.jar')
        compile files('libs/cloudssp_videoads_xx.jar')
        compile files('libs/cloudssp_imageloader_xx.jar')
	}
	
	or
	
	dependencies {
	     compile 'com.cloudtech:ads:2.4.0'
	     compile 'com.cloudtech:videoads:2.4.0'
	     compile 'com.cloudtech:imageloader:2.4.0'
	}
```

* Update the AndroidManifest.xml for Rewarded Video:

``` xml    

    <activity android:name="com.cloudtech.videoads.view.CTInterstitialActivity"
            android:configChanges="keyboard|keyboardHidden|orientation|screenLayout|uiMode|screenSize|smallestScreenSize"/>

```

* Setup the UserID

  You should set the userID for s2s-postback before preloading the RewardedVideo function call


```java
    CTServiceVideo.setUserId("custom_id");
```

* Preload the RewardedVideo

    It‘s best to call this interface when you want to show RewardedVideo ads to the user. This will help reduce latency and ensure effective, prompt delivery of ads


``` java
     CTServiceVideo.preloadRewardedVideo(getContext(), “your slotid”,
               new VideoAdLoadListener() {
                   @Override
                   public void onVideoAdLoaded(CTVideo ctVideo) {
                        Log.e(TAG, "onVideoAdLoaded: ");
                   }

                   @Override
                   public void onVideoAdLoadFailed(CTError error) {
                        Log.e(TAG, "onVideoAdLoadFailed: " + error.getMsg());
                   }
               });

```

* Show Rewarded Video ads to Your Users


Before showing the video, you can request or query the video status by calling:

```java
    boolean available = CTServiceVideo.isRewardedVideoAvailable(ctVideo);
```

Once you get an available Reward Video, you are ready to show this video ad to your users by calling the showRewardedVideo() method as in the following example:


```java
 CTServiceVideo.showRewardedVideo(ctVideo, new VideoAdListener() {
             @Override
             public void videoPlayBegin() {
                 Log.e(TAG, "videoPlayBegin: ");
             }
    
             @Override
             public void videoPlayFinished() {
                 Log.e(TAG, "videoPlayFinished: ");
             }
    
             @Override
             public void videoPlayError(Exception e) {
                 Log.e(TAG, "videoPlayError: ");
             }
    
             @Override
             public void videoClosed() {
                 Log.e(TAG, "videoClosed: ");
             }
    
             @Override
             public void onRewardedVideoAdRewarded(String rewardName, String rewardAmount) {
                 Log.e(TAG, "onRewardedVideoAdRewarded: ");
             }
         });


```

*  Reward the User

The SDK will fire the onRewardedVideoAdRewarded event each time the user successfully completes a video. The RewardedVideoListener will be in place to receive this event so you can reward the user successfully.

The Reward object contains both the Reward Name & Reward Amount of the SlotId as defined in your Cloudmobi ssp:

```java

public void onRewardedVideoAdRewarded(String rewardName, String rewardAmount) {
      //TODO - here you can reward the user according to the given amount
       Log.e(TAG, "onRewardedVideoAdRewarded: ");
   }
    
```

## <a name="error">Error Code For SDK</a>

| Error Code                        | Description                              |
| --------------------------------- | ---------------------------------------- |
| ERR\_000\_TRACK                   | Track exception                          |
| ERR\_001\_INVALID_INPUT           | Invalid parameter                        |
| ERR\_002\_NETWORK                 | Network exception                        |
| ERR\_003\_REAL_API                | Error from Ad Server                     |
| ERR\_004\_INVALID_DATA            | Invalid advertisement data               |
| ERR\_005\_RENDER_FAIL             | Advertisement render failed              |
| ERR\_006\_LANDING_URL             | Landing URL failed                       |
| ERR\_007\_TO_DEFAULT_MARKET       | Default Landing URL failed               |
| ERR\_008\_DL_URL                  | Deep-Link exception                      |
| ERR\_009\_DL_URL_JUMP             | Deep-Link jump exception                 |
| ERR\_010\_APK_DOWNLOAD_URL        | Application package download failed      |
| ERR\_011\_APK_INSTALL             | Application install failed               |
| ERR\_012\_VIDEO_LOAD              | Load the video exception                 |
| ERR\_013\_PAGE_LOAD               | HTML5 page load failed                   |
| ERR\_014\_JAR_UPDATE_VERSION      | Update jar check failed                  |
| ERR\_015\_GET_GAID                | Cannot get google advertisement id - GAID retrieval failed |
| ERR\_016\_GET_AD_CONFIG           | Cannot get the account configuration or template |
| ERR\_017\_INTERSTITIAL_SHOW_NO_AD | Tried to load the interstitial advertisement, but the advertisement is not ready/loading |
| ERR\_018\_AD_CLOSED               | Ad slotId has been closed                |
| ERR\_999\_OTHERS                  | All other errors                         |








