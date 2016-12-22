BASIS
=====

Getting Started: official documentation and guides
--------------------------------------------------

The documentation needed to work with Android can be found in the following url:

[https://developer.android.com/index.html](https://developer.android.com/index.html)

In this web page can find three different sections:

   - **Design**: You can find information on Material Design, design patterns, animations, UI design,...

	[https://developer.android.com/design/index.html](https://developer.android.com/design/index.html)

   - **Develop**: You can find information on how to develop on Android. Using Android Studio, information from different APIs, examples,...

	[https://developer.android.com/develop/index.html](https://developer.android.com/develop/index.html)

	There is a section with practical examples that are guided and taught step by step:

	[https://developer.android.com/develop/index.html](https://developer.android.com/develop/index.html)

   - **Distribute**: You can find information to publish your app in the Google Play Store and tips to increase downloads and make your application more visible.

	[https://developer.android.com/distribute/index.html](https://developer.android.com/distribute/index.html)

You can also find free courses taught by Google on the Udacity web page:

[https://www.udacity.com/courses/android](https://www.udacity.com/courses/android)

<br></br>
How to install and update Android Studio, JDK, SDK
--------------------------------------------------

### Installation
Android Studio provides the fastest tools for building apps on every type of Android device.

To install Android Studio it is necessary to download java JDK and Android Studio. Both things are available in the links below:

   - [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
   - [Android Studio](http://developer.android.com/sdk/index.html)

### SDK Manager
Android SDK Manager separates SDK Tools, platforms and other components into packages of easy access and management.
Once you have Android Studio installed, it is easy to keep the Android Studio IDE and the Android SDK Tools updated with automatic updates and the SDK Manager.  

The following list of packages are necessary for the development of an android project.  

*<ins>Mandatory packages</ins>*  
   - **Android SDK Build Tools**: Includes tools to build Android apps.  
   - **Android SDK Platform-tools**: Includes various tools required by the Android platform, including the adb too.
   - **Android SDK Tools**: Includes essential tools such as the Android Emulator and ProGuard.
   - **Android SDK Platform**: At least one platform is required in your environment so you are able to compile your application. In order to provide the best user experience on the latest devices, use the latest platform version as your build target.  

*<ins>Recommended packages</ins>*  
   - **Android Support Repository**: Includes local Maven repository for Support libraries, which provide an extended set of APIs that are compatible with most versions of Android.
   - **Google Repository**: Includes local Maven repository for Google libraries, which provide a variety of features and services for your apps.
   - **Intel o ARM System Images**: The system image is required in order to run the Android Emulator.  

***Edit or add SDK tool sites***  
You can add other sites that host their own tools, then download the packages from those sites.

### Update IDE and Change Channels
Android Studio notifies you with a small bubble dialog when an update is available for the IDE, but you can manually check for updates by clicking **Help >Check for Update** (on Mac, **Android Studio > Check for Updates**).

Updates for Android Studio are available from the following release channels:

   - **Canary Channel**: These are bleeding-edge releases, updated roughly weekly. Although these builds are subject to more bug, the do get tested and we want to offer early access so you can try new features and provide feedback. This channel is not recommended for production development.
   - **Dev Channel**: These are hand-picked canary builds that survived a full round of internal testing.
   - **Beta Channel**: These are release candidates based on stable canary builds, released to get feedback before going into the stable channel.
   - **Stable Channel**: The official stable release that is available for download.

For your production Android projects you should use a version of Android Studio downloaded from the Stable Channel. If you would like to try one of the other channels (Canary, Dev o Beta) while still using the Stable build, you can safely install a second version of Android Studio by downloading the preview build from [tools.android.com](http://tools.android.com/download/studio).