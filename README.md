# New millet smart family Android app Free installation plug-in development manual

## Important changes

-Add RBI specification
 [打点统计规范](打点统计.pdf)

- Add a generic open device security settings interface

1. Call the new settings page interface, the incoming parameter "security_setting_enable", Open the security settings option
```
        Intent intent = new Intent();
        intent.putExtra("security_setting_enable",true);
        mHostActivity.openMoreMenu2(menus, true, REQUEST_MENUS, intent);
```

2.Call the following interface on the page that needs to verify pincode onCreate ()
        
```
  mHostActivity.enableVerifyPincode();
  
```

-Update the WeChat sdk library, if you use the following interface plug-in to update the plug-in, otherwise crash

`` ``
  / **
     * ApiLevel: 25 after upgrading the WeChat sdk, useful to this interface must update the plugin sdk adapter, release the new plugin and modify minPluginSdkApiVersion to 25
     * ApiLevel: 20 Create a micro-mail interface for the m app app
     * /
    public abstract IWXAPI createWXAPI (context context, boolean bl);
```

## Latest changes

- [new and more menu interface] (more menu interface .md)
XmPluginHostApi
`` ``
  / **
      * ApiLevel: 25
      * Encoding generates two-dimensional code pictures
      * @param barcode two-dimensional code information
      * @param width picture width
      * @ param height picture height
      * @return returns a two-dimensional code for the ARGB_8888 format
      * /
     public abstract Bitmap encodeBarcode (String barcode, int width, int height);
 
     / **
      * ApiLevel: 25
      * Decode two-dimensional code picture
      * @param bitmap Two-dimensional code picture, which must be in ARGB_8888 format
      * @return returns two-dimensional code information
      * /
     public abstract String decodeBarcode (Bitmap bitmap);
`` ``

- Add a two-dimensional code interface

IXmPluginHostActivity

`` ``
 / **
     * ApiLevel: 27 more menu new standard, from the drop-down menu, default there
     * Smart scene scence_enable
     * Common settings common_setting_enable
     Help and feedback help_feedback_enable
     * /
   public abstract void openMoreMenu2 (ArrayList <MenuItemBase> menus,
                                            boolean useDefault, int requestCode, Intent params);
`` ``

IXmPluginHostActivity

`` ``
/ **
     * ApiLevel: 25 Jump to two-dimensional code scan page
     * @param bundle request parameters, you can wear null @see # Activity.startActivityForResult (Intent, requestCode)
     * @param requestCode @see # Activity.startActivityForResult (Intent, requestCode)
     * /
    public abstract void openScanBarcodePage (Bundle bundle, int requestCode);
`` ``

- Add water and electricity gas payment interface

IXmPluginHostActivity

`` ``
/ **
     * ApiLevel: 23 Jump water and electricity gas payment page
     * @param type 0: water and electricity to pay the main page 1: water 2: electricity 3: gas
     * @param latitude latitude
     * @param longitude longitude
     * /
    public abstract void openRechargePage (int type, double latitude, double longitude);
`` ``

- Add water and electricity gas payment balance query interface

XmPluginHostApi
`` ``
 / Api level: 23
     *
     * Check the balance of hydropower gas
     *
     * @param type 1: water 2: electricity 3: gas
     * @param latitude latitude
     * @param longitude longitude
     * @param callback return query balance Json // {"balance": 300, "updateTime": 1465781516, "rechargeItemName": "Zhengzhou gas fee"}
     * When null is returned, there is no binding machine number
     * /
    public abstract void getRechargeBalances (int type, double latitude, double longitude, Callback <JSONObject> callback);

`` ``

- Add to the plugin to send the broadcast interface

`` ``
IXmPluginHostActivity
  / **
      ApiLevel: 22
      * Send a broadcast to the device and send it to IXmPluginMessageReceiver.handleMessage
      * /
     public Intent getBroadCastIntent (DeviceStat deviceStat) {
         Intent intent = new Intent ("com.xiaomi.smarthome.RECEIVE_MESSAGE");
         intent.putExtra ("device_id", deviceStat.did);
         return intent
     }
`` ``
- plugin the build.gradle inside do not use the following libraries, these libraries depend on the script has been added
`` ``
    compile 'com.android.support:support-v4:23.1.1'
    compile 'com.android.support:support-v13:23.1.1'
    compile 'com.android.support:recyclerview-v7:23.1.1'
    compile 'com.android.support:appcompat-v7:23.1.1'
`` ``
- Added using the help interface
`` ``
IXmPluginHostActivity
    / **
     * ApiLevel: 22 Jump to the help page
     * /
    public abstract void openHelpActivity ();

`` ``

- Jump to the authorization management page
`` ``
   / **
     * ApiLevel: 44
     * Jump to the authorization management page
     * @param activity
     * @param did
     * /
    public abstract void gotoAuthManagerPage (Activity activity, String did);
`` ``
- Shared permission control
`` ``
Class BaseDevice ApiLevel: 20
Shared device
public boolean isShared ()
Read-only shared device, can not control, is compatible with the old version while isShared () returns true
public boolean isReadOnlyShared ()
`` ``
- plug-in run a separate process, exit the plug-in after 30s, automatically withdraw from the plug-in process
- new plugin sdk using Android development
- Automatically generate plug-in project script gen_plug.py
- Run gradle install to install the plugin automatically and start the app

A




## plugin development

### Prepare work before development
- login [smart home open platform] (https://open.home.mi.com)
- Apply for developer account
- register new product, record equipment model
- Create a signing certificate
- create plug-in information, submit the certificate md5 information to millet

`` ``
Signature file md5 information acquisition, need to remove: number

keytool -list -v -keystore keyFilePath -storepass keypassword -keypass keypassword
`` ``
### Configure the account whitelist
If the developer if not registered with the developer's millet account login, you need to configure the current millet account for collaborative development or test white list
! [] (./md_images/391501033771_.pic.jpg)
### Install the development version of the smart home app

Smart home application store version of the app does not support local development debugging, need to install sdk directory smart home app

### Project directory structure

- [github] (https://github.com/MiEcosystem/NewXmPluginSDK) update SDK code

- plug-in project placed in the plugProject directory, as shown below, you can place multiple plug-in works, pay attention to the plug-in project directory structure
You can also manage other git library projects directly in the plugProject directory, such as the plugin instance project [NewPluginDemo] (https://github.com/MiEcosystem/NewPluginDemo), direct clone in the plugProject directory

`` ``
cd plugProject
git clone https://github.com/MiEcosystem/NewPluginDemo.git
`` ``

! [] (./md_images / gradle_project.png)

### Older sdk plugin works to migrate to new sdk

Execute the sdk directory under the python script move_plug.py as follows

`` ``
python move_plug.py oldPlugPath
`` ``

### Create a new plug-in project
- execute the python script in the SDK directory gen_plug.py
  Note DevelopId for the application of millet developer account, millet account, not the phone number

`` ``
python gen_plug.py model userid
`` ``
### How to get developer Id
! [] (./md_images / developid_1.jpg)



! [] (./md_images/ developid_2.jpg)

### Configure the plugin signature file
All plug-ins need to be signed for verification on a smart home app
- Modify the signature information of the plugin project build.gradle

`` ``
    signingConfigs {
        release {
            storeFile new File ("$ {project.projectDir} /keystore/key.keystore")
            storePassword 'mihome'
            keyAlias ​​'mihome-demo-key'
            keyPassword 'mihome'
        }
    }

    buildTypes {
        debug {
            debuggable true
            signingConfig signingConfigs.release
        }
        release {
            minifyEnabled true
            shrinkResources false
            zipAlignEnabled true
            proguardFiles getDefaultProguardFile ('proguard-android.txt'), 'proguard-rules.pro'
            signingConfig signingConfigs.release
        }
    }
`` ``

### Configure the plugin to compile the script

- modify the plugin project build.gradle at the end of the add

`` ``
apply from: "${project.rootDir.absolutePath}/plug.gradle"

`` ``

### Add plugin dependent items
If the plugin has other project dependencies, add it to the complieProject property as follows

`` ``
project.ext.set ("complieProject", [": demolib"])
`` ``
Dependent on the project structure is as follows

! [] (./md_images / gradle_lib.png)

Depends on the jar library and native so placed in the libs directory

### plugin compiles run

- through the gradle instruction to compile and run, in the plug-in sdk directory

`` ``
gradle tasks
`` ``
You can see the installation plug-in task, as shown below

! [] (./md_images / gradle_tasks.png)

`` ``
./gradlew install install the release configuration plugin
./gradlew installRelease install the release configuration plugin
./gradlew installDebug Install the debug configuration plugin

If there are multiple plug-in projects, the above instructions will install all plug-ins, specify the installation of a plug-in project, such as plug-in Xiaomi_demo

./gradlew installXiaomi_demo Install Run the release configuration plugin
./gradlew installXiaomi_demoRelease Install Run the release configuration plugin
./gradlew installXiaomi_demoDebug Install the debug configuration plugin
`` ``

### Associated pluglib library source code
Plug-in development, found in the pluglib function variable name was renamed, no comments, you can link the following method pluglib source code to see the source code in the comments and variable name
Click on the system to decompile the upper right corner of the source file

! [] (./md_images/ attach_sources_0.png)

Then select libs_ex under the pluglib-sources.jar

! [] (./md_images/ attach_sources_1.png)

### Debugging plugins
Install the plug-in, it will automatically start the smart home app, click android studio debugging button, select com.xiaomi.smarthome: plugin process, as shown below button, you can interrupt the code in the plug-in code

! [] (./md_images / gradle_debug.png)

### upload plug-in to the smart home background, apply on-line

After the completion of the development, the test can be applied on-line, compiled a good installation package plug-in directory / build / output / apk below
In addition to functional testing through, we must pay attention to the memory test, in principle, after exiting the plug-in page, the plug-in need to exit all the background thread, the release of all the memory resources, in particular the Activity object memory leak
On-line review, will be specifically for these two tests.


## plugin instance test

[Plugin example project] (https://github.com/MiEcosystem/NewPluginDemo)
Smart home app test account, install the plug-in, click the millet development board equipment, you can see the effect of examples
`` ``
Username: 923522198
Password: 123asdzxc
`` ``

## rely on other jar packages
If you want to rely on other jar, then do not need to modify gradle, jar directly into the. / Libs directory below

## There is no real equipment yet
If the developer does not have a real device, would like to skip the connection process directly open the plug-in, you can: in the meter app developer option to open the "whether to open no device development mode", and then enter the manual add device page, You can enter the device to add the page

## other links

- [frame description] (frame description .md)
- [server deployment] (server deployment .md)
- [plugin development] (plugin development .md)
- [Development interface description, please focus] (./ api / README.md)
- [Bluetooth specification] (smart home Bluetooth specification. Md)
- [plugin example project] (https://github.com/MiEcosystem/NewPluginDemo)


## development problems encountered

- plug-in and try to avoid the same name and common_ui inside the same name, otherwise the resources in the plug-in will replace the common_ui inside the resources
- do not support Serializable temporarily, please use Parcelable instead
- androi-support - *. jar library does not require plugin introduction, has been added in public configuration
- plug-in and app used in the same library, the latest development version of the app has to solve this problem, online app, or need to keep the library version of the same, and do not confuse

------

Develop contact information
wx: zhaomingnpu
