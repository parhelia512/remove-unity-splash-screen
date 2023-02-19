# Remove Unity Splash Screen

## Introduction

![Unity Splash Screen](./img/IMG_Unity_Splash_Screen.gif "Unity Splash Screen")

> Well, pretty damn controversial.., huh?

Are you a Unity Developer? Did you bother this logo appear in your game? Didn't have much money to buy the license?
No worry, I will share to you how to remove Unity splash screen for free without any license $hit!

I haven't tried on some platforms because I haven't installed the dependencies needed to build games on those platforms in Unity.
But, if you manage to remove the Unity splash screen using the method below, please make an [Issue](https://github.com/kiraio-moe/remove-unity-splash-screen/issues "Issue") or contact me through [my social media](https://github.com/kiraio-moe) and let me know the details so I can get it straight away updating this thread.

Without further ado, let's do it!

## Disclaimer

By doing this, of course you violate the applicable terms of Unity Technologies. #DWYOR!

## Prerequisite

Install the following tools before proceed to [Step-by-step section](#step-by-step):

### General (Required)

- [Unity Asset Bundle Extractor (UABE)](https://github.com/SeriousCache/UABE/releases "Unity Asset Bundle Extractor")
- [HxD Hex Editor](https://mh-nexus.de/en/hxd/ "HxD Hex Editor")

### Platform Specific

Install the following tools if you wanna work with another platform:

- Android
  - [APKTool GUI](https://github.com/AndnixSH/APKToolGUI/releases "APKTool GUI")
- WebGL
  - [PeaZip](https://peazip.github.io/ "PeaZip") (Optional)

## Step-by-step

### Universal

#### unity default resources

This is the easiest way to remove Unity splash screen. You just need to edit `unity default resources` file in your build.  
PC: `GameTitle_Data/Resources/`.  
Android: `assets/bin/Data/`. Steps for decompile & recompile APK file, please refer to [Android section](#android).  

- Open `unity default resources` file using UABE.
- Search for `UnitySplash-cube` which is type of Texture2D then view the data.

  ![Searching unity default resources file](./img/IMG_Universal_01.png "Searching unity default resources file")

- Set the `m_Width` and `m_Height` variable to `0` by double click it.

  ![Set m_Width and m_Height variable to 0](./img/IMG_Universal_02.png "Set m_Width and m_Height variable to 0")

- Don't forget to set the `Draw Mode` to `All Sequential` and Unity logo duration to 2 second, Player will not notice that blank Made with Unity splash. If the `Draw Mode` is `Unity Logo Below`, it will resulting a weird splash screen where your logo isn't in center but on top.

  ![Setting Draw Mode & Logo Duration](./img/IMG_Universal_03.png "Setting Draw Mode & Logo Duration")

- DONE! Save then replace original `unity default resources` file.

### PC, Mac, Linux Standalone

- Prepare your game that has been built.

  ![Target Unity game](./img/IMG_PC_01.png "Target Unity game")

- Go to the `Game Title_Data` folder and **backup** the `globalgamemanagers` file in case of errors.
- Open the `globalgamemanagers` file using Unity Asset Bundle Extractor (UABE).
- Click `globalgamemanagers (Assets)` in Files and Components. Select `Unnamed asset` which is of type **`PlayerSettings`** then click **View Data** and...

  ![View Player Settings](./img/IMG_PC_02.png "View PlayerSettings")

- BOOM! Error................

  ![View Player Setings Error](./img/IMG_PC_03.png "View PlayerSettings Error")

  This happens because UABE didn't support parsing those particular data.

- We will fix that problem later. For now, let's see how to work with UABE if your Unity version is fully supported by UABE.  
  As you can see, we can access PlayerSettings. If we change the value of `m_ShowUnitySplashScreen` to `false` (by double clicking) then the entire Splash Screen will not appear when starting the game (the **Made with Unity** logo will also not appear automatically) and as you can guess, if we change the value of `m_ShowUnitySplashLogo` to `false` then the **Made with Unity** logo will not appear.

  ![Accessing PlayerSettings Unity 2019](./img/IMG_PC_04.png "Accessing PlayerSettings Unity 2019")

  But, by just removing the **Made with Unity** logo will not work if the **Made with Unity** splash logo is still listed in `m_SplashScreenLogos`. To fix this, we can simply delete the array item that contains the **Made with Unity** logo. Great!

  > Not tested though. But, it's a great way to ensure that we completely remove the logo.

  ![Removing Made with Unity logo from Splash Screen Logos array](./img/IMG_PC_05.png "Removing Made with Unity logo from Splash Screen Logos array")

- Now let's move on to other settings. Move to **Asset List** tab and open `BuildSettings`:

  ![Viewing BuildSettings Unity 2019](./img/IMG_PC_06.png "Viewing BuildSettings Unity 2019")

- Select `hasPROVersion` and change the value to `true`:

  ![Enabling Pro Version](./img/IMG_PC_07.png "Enabling Pro Version")

- Save your changes (IDK why **Apply** does nothing). UABE cannot overwrite the file that being edited, so just save it as `globalgamemanagers-mod`.

  ![Save changes](./img/IMG_PC_08.png "Save changes")

- Close `globalgamemanagers` on UABE or simply close UABE app, delete the original `globalgamemanagers` file or rename to something else then rename `globalgamemanagers-mod` to `globalgamemanagers`.
- Try running the game and be surprised!

#### Fix for Unsupported Version

If you've read all the steps above, you should know what the problem is. UABE can't deserialize some or entire data of ``globalgamemanagers``. That's why the Hex Editor is coming for! We will edit boolean ``m_ShowUnitySplashScreen`` and ``hasPROVersion`` manually using HxD.

- Open `globalgamemanagers` file using HxD.
- First, we remove the splash screen. Search your **Company Name** or **Product Name** (in my case it's `kiraio` and `Bungaku`) until you get like the image below.  
  Notice the **first Question Mark (?)** after the product name, right after that there's a hex value `01 01` (in my case, it's in offset: 1060 and 1061). If you change the hex value of the first `01` to `00` then the Splash Screen will be disabled, while the following `01` sets whether the **Made with Unity** logo will appear or not.

  ![Find Splash Screen boolean](./img/IMG_PC_09.png "Find Splash Screen boolean")

- Now we need to switch the ``hasPROVersion`` boolean to ``true``. Search the name of a scene that you included in the build or your Unity version until you get like the image below. The highlighted hex value (offset: E7A4, 20 offset before the Unity version) is the boolean ``hasPROVersion``. Change to ``01`` value to enable it.

  ![Find hasPROVersion boolean](./img/IMG_PC_10.png "Find hasPROVersion boolean")

- Boolean Unity splash screen (`m_ShowUnitySplashScreen`) was discovered by the YouTube channel **Awesomegamergame** in his video <https://www.youtube.com/watch?v=xvh0AeZCX9E>. But the way he just change the Unity splash screen boolean value to false doesn't work in other versions of Unity. I think this thread as a complement to his tutorial.

### Android

- Prepare your Unity Android game.

  > In the image, I used the `.apks` format which is basically just a `.zip` file, so I just extracted it and got 2 files namely `base.apk` and `split_config.arm64_v8a.apk`. After I finished doing the step below, I archived files again to `.zip` and changed the name of the extension to `.apks`.

  ![Prepare a Unity game](./img/IMG_Android_01.png "Prepare a Unity game")

- Open APKToolGUI (Make sure you meet [APKToolGUI requirements](https://github.com/AndnixSH/APKToolGUI#requirements "APKToolGUI requirements")).  
  Drag and Drop the APK file (If you using ``.apks`` file, just drop which you guys think is the main APK, in my case is `base.apk`) to the APK File section. APKToolGUI will automatically decompile the APK.

  ![Decompile APK using APKToolGUI](./img/IMG_Android_02.png "Decompile APK using APKToolGUI")

- Go to the decompiled APK folder then go to `assets/bin/Data` folder, you will find the `globalgamemanagers` file.

  ![Find globalgamemanagers file](./img/IMG_Android_03.png "Find globalgamemanagers file")

- Do the same as in [PC, Mac, Linux Standalone section](#pc-mac-linux-standalone "PC, Mac, Linux Standalone section").
- After finish editing `globalgamemanagers`, go to APKToolGUI and re-compile the decompiled APK folder.

  ![Recompile APK](./img/IMG_Android_04.png "Recompile APK")

- DONE!
- For those who have uploaded the game to the App Store (such as Google Play), make sure to sign your APK consistently. If not, when you update your game that is already installed on Android, an error `Signature is inconsistent with an existing application` will appear.

  > Not fully tested!

  For new apps, it might be time for you to start signing APKs consistently.

  > You can generate a Keystore in Unity, go to `Project Settings > Publishing Settings > Manage Keystore` (Make sure you have switched to Android platform).

  ![Signing APK](./img/IMG_Android_05.png "Signing APK")

### Alternative: data.unity3d

I just discovered that my Unity Android game is using ``data.unity3d`` file to store the ``globalgamemanagers`` file like in [WebGL](#webgl "WebGL"):

![data.unity3d file](./img/IMG_Android_06.png "data.unity3d file")

The steps to remove the splash screen is same as before. The boolean ``m_ShowUnitySplashScreen`` is same after the first question mark:

![Boolean m_ShowUnitySplashScreen location](./img/IMG_Android_07.png "Boolean m_ShowUnitySplashScreen location")

While boolean ``hasPROVersion`` is slightly different with ``globalgamemanagers`` in [PC, Mac, Linux Standalone](#pc-mac-linux-standalone "PC, Mac, Linux Standalone").  
12 offset before the 'cutted' Unity version:

![Signing APK](./img/IMG_Android_08.png "Signing APK")

### WebGL

I thought I would give up on the WebGL build because I think the `globalgamemanagers` file was in `WebGL.wasm` file which is currently there's no good tool to decompile Web Assembly.  
But after digging deeper, I found a hint that the `globalgamemanagers` file is in the `WebGL.data` file, in the end I was able to remove the Unity splash screen in the WebGL build :)

- Prepare your Unity WebGL game, then go to `Build` folder.

  ![WebGL Build folder structure](./img/IMG_WebGL_01.png "WebGL Build folder structure")

- If you build using Brotli (`.br`) or Gzip (`.gzip`) compression, you can install [PeaZip](https://peazip.github.io/ "PeaZip") or any archiving tool to extract `WebGL.data.br` or `WebGL.data.gzip` and get the `WebGL.data` file. After removing the splash screen, you can archive it back into the original extension.

  > Did you know? You can unpack the content of `WebGL.data` file using [Asset Studio](https://github.com/Perfare/AssetStudio/releases "Asset Studio"), there you will get this:
  >
  > ![WebGL.data contents](./img/IMG_WebGL_02.png "WebGL.data contents")
  >
  > The `globalgamemanagers` file is inside `data.unity3d` file. I tried to open it in UABE but UABE unable to open it. If you know how to unpack and repack `data.unity3d` file, let me know!  
  >
  > Even I can unpack `WebGL.data`, still I didn't know how to repack it back :(

- With all the limitations above, there is no other way but to edit the `WebGL.data` file directly. Open the `WebGL.data` file using HxD (Don't forget to backup the original file!).
- Search your **Company Name** or any of string I highlighted below:

  ![Searching boolean splash screen](./img/IMG_WebGL_03.png "Searching boolean splash screen")

- This time, boolean ``m_ShowUnitySplashScreen`` isn't after the first question mark that appear after the **Product Name**, but after the **Double Quote (")**. Change the hex value (in my case, it's offset: 59CEF7) to ``00`` to disable the splash screen.

  ![Boolean splash screen location](./img/IMG_WebGL_04.png "Boolean splash screen location")

  > If you change the following highlighted hex value: ``01`` to ``00``, will resulting in some sort of Active Input Handler error. So, don't do that!

- Now, we need to search where boolean ``hasPROVersion`` located. This is the trickiest part...  
  I found a hint to search the boolean using the first 40 characters (from the front) of ``m_AuthToken`` string.

  ```txt
  077fc7b716f3938b594d7ec67f4107f3e125c97d
  ```

  I got it from the ``globalgamemanagers`` file of my game that was built on another platform.

  ![Searching hasPROVersion boolean using m_AuthToken string](./img/IMG_WebGL_05.png "Searching hasPROVersion boolean using m_AuthToken string")

  ``m_AuthToken`` is different for every installed machine. So, you need to build your game into another platform first. If you dilligent enough, you can manually search the string from offset ``5A3E00`` to offset ``5A4000`` or use the string I highlighted below:

  ![Searching hasPROVersion boolean using hinted string](./img/IMG_WebGL_06.png "Searching hasPROVersion boolean using hinted string")

- I tried reversing every hex that has ``00`` or ``01`` value before the string we are looking for.  
  Finally, I found it! The red highlighted hex (offset: 5A3EBA) which has a value of ``01`` (I have changed it to ``00`` in the picture) is the boolean ``hasPROVersion``, while the black highlighted hex (offset: 5A3EBE) is the boolean ``isDebugBuild``.
  > I'm confused why ``00`` which should be ``false`` becomes ``true`` and vice versa.

  ![hasPROVersion boolean location](./img/IMG_WebGL_07.png "hasPROVersion boolean location")

- Save & DONE!

## Notes

- There is no difference affecting the splash screen in the `globalgamemanagers` file between Development builds and Release builds.

## Result

- **Android**

  ![MOD Result Android](./img/IMG_Remove_Unity_Splash_Screen_Result_Android.gif "MOD Result Android")

- **PC**

  ![MOD Result PC](./img/IMG_Remove_Unity_Splash_Screen_Result_PC.gif "MOD Result PC")

- **WebGL**

  ![MOD Result WebGL](./img/IMG_Remove_Unity_Splash_Screen_Result_WebGL.gif "MOD Result WebGL")

Tested and work on games built with the following versions of Unity:

### Unity 2020.3.39f1 (LTS)

- PC, Mac, Linux Standalone
- Android
- WebGL

### Unity 2019.4.12f1 (LTS)

- Android

Worked? Please, give it a star ⭐ and tell everyone by making an [Issue](https://github.com/kiraio-moe/remove-unity-splash-screen/issues "Issue").  
Something is missing? Misinformation? Make an [Issue](https://github.com/kiraio-moe/remove-unity-splash-screen/issues "Issue").  
Have another way in mind? Make a [Pull Request](https://github.com/kiraio-moe/remove-unity-splash-screen/pulls "Pull Request").

## Huge Thanks

Many thanks to this fellas:

- [SeriousCache](https://github.com/SeriousCache "SeriousCache") for [Unity Asset Bundle Extractor (UABE)](https://github.com/SeriousCache/UABE "Unity Asset Bundle Extractor")
- Maël Hörz for [HxD Hex Editor](https://mh-nexus.de/en/hxd/ "HxD Hex Editor")
- [iBotPeaches](https://github.com/ibotpeaches "iBotPeaches") for [Apktool CLI](https://ibotpeaches.github.io/Apktool/ "Apktool CLI")
- [INF1NUM](https://github.com/INF1NUM "INF1NUM") | [AndnixSH](https://github.com/AndnixSH "AndnixSH") for [APKToolGUI](https://github.com/AndnixSH/APKToolGUI "APKToolGUI")
- [Awesomegamergame](https://www.youtube.com/@Awesomegamergame "Awesomegamergame YouTube channel") for [discovering splash screen offset](https://www.youtube.com/watch?v=xvh0AeZCX9E)
- and others that I didn't mention...
