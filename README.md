# Android best practices
<!-- MarkdownTOC depth=0 autolink=true bracket=round --> 

 This is how we work with Android at BEEVA.

![logo](statics/beeva_android.png)

- [Basis](#basis)
- [Project Configuration](#project-configuration)
- [Application Architecture](#application-architecture)
- [User Interface and Visuals](#user-interface-and-visuals)
- [Coding Style](#coding-style)
- [Libraries](#libraries)
- [Testing](#testing)
- [Performance](#performance)
- [Security & Privacy](#security--privacy)
- [Background jobs](#background-jobs)
- [Interaction and Engagement](#interaction-and-engagement)
- [Persistence & Storage](#persistence--storage)
- [Analytics & Crash Logs](#analytics--crash-logs)
- [Continuous Integration](#continuous-integration)
- [Deployment & Publishing](#deployment--publishing)
- [Version Control](#version-control)

<!-- /MarkdownTOC -->

## Basis

### Getting Started: official documentation and guides

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

### How to install and update Android Studio, JDK, SDK

#### Installation
To install Android Studio it is necessary to download java JDK and Android Studio. Both things are available in the links below:

   - [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
   - [Android Studio](http://developer.android.com/sdk/index.html)

#### SDK Manager
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

**Edit or add SDK tool sites**
You can add other sites that host their own tools, then download the packages from those sites.

#### Update IDE and Change Channels
Android Studio notifies you with a small bubble dialog when an update is available for the IDE, but you can manually check for updates by clicking **Help >Check for Update** (on Mac, **Android Studio > Check for Updates**).

Updates for Android Studio are available from different channels (Canary, Dev, Beta and Stable). For production Android projects you should use a version of Android Studio downloaded from the Stable Channel (the official stable release that is available for download). If you would like to try one of the other channels (Canary, Dev o Beta) while still using the Stable build, you can safely install a second version of Android Studio by downloading the preview build from [tools.android.com](http://tools.android.com/download/studio).

## Project Configuration

###Configure build variants in gradle


####Establish applicationId in build.gradle  
The `applicationId`, `versionCode` and `versionName` should be configured in the `build.gradle` of the main module.

```groovy
android {
   // ...
   defaultConfig {
       applicationId "com.beeva.myapplication"
       versionCode 1
       versionName "1.0"
  // ...
   }
```

It is recommended to have a different `applicationId` for the apk that is in debug mode. To do this it is necessary to include the following code:

```groovy
buildTypes {   
    debug {
      applicationIdSuffix ".debug"
      versionNameSuffix '-DEBUG'
    }
    // ...
}
```
####Configuration for the release version 
Proguard is activated by default so you will only need to configure it if necessary. Proguard acts only upon code and not resources such as images. To remove these resources that are not used you need to set the option [shrinkResources](https://developer.android.com/studio/build/shrink-code.html?hl=es-419#shrink-resources) to _true_:
```groovy
buildTypes {
    release {
      shrinkResources true
      minifyEnabled true
      proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
      // ...
    }
    // ...
}
```
####Configuration of the keystore file  
We can add the configuration needed to sign the apk that is going to be publish to the `build.gradle` file:
```groovy
signingConfigs {
    release {
      storeFile file("myapp.keystore")
      storePassword "12345"
      keyAlias "alias_name"
      keyPassword "67890"
    }
}
```
For safety, passwords must not be on this file. Instead, we will generate a file named `gradle.properties` to store them:
```
STORE_PASSWORD=12345
ALIAS=alias_name
KEY_PASSWORD=67890
```
This way, the configuration would look like this:
```groovy
signingConfigs {
    release {
      storeFile file("myapp.keystore")
      storePassword STORE_PASSWORD
      keyAlias ALIAS
      keyPassword KEY_PASSWORD
    }
}
```
This way we can store other sensible data that we do not want to commit to the repository.

####Dependencies  
In general, it is important to obtain the dependencies of our project from a repository and not from a local .jar file since it will be easier to update our dependency to its ultimate version, specially in projects where there is more than one developer, which is the normal case.
It is recommended to have a separate file with the version of each dependency so that if a different module needs to use one of these dependencies, the version used will be the same as the one used in the main module for the same dependency.

```groovy
// dependencies.gradle file
ext {
  supportLibraryVersion = ‘25.1.0’
  butterknifeVersion = ‘8.4.0’
  // ...
}

// build.gradle file
dependencies {
  // ...
  compile "com.android.support:cardview-v7:$supportLibraryVersion"
  // ...
}
```
An exact version must be indicated, i.e. "25.1.0", and not a range, i.e. "25.+", so that we do not find an unexpected change in our dependency.  

*<ins>Google Play Services and Firebase</ins>*  
We must not import all the dependencies from Google Play Services and Firebase. These libraries contain all the native packages for Android, so, if we import them, we could pass the 65K methods limit and we would be importing code that we won’t probably need or use. To avoid this, we must import only the libraries that we are really going to use. You can check the libraries in Google Play Services [here](https://developers.google.com/android/guides/setup#add_google_play_services_to_your_project) and the ones in Firebase [here](https://firebase.google.com/docs/android/setup?hl=en#available_libraries).

###Structure of folders and packages

####Code
The structure of packages we use will be related to the [architecture](#application-architecture) we implement.

####Resources
Images, layouts, strings, colors and dimensions are some of the resources we have in Android. These resources have their own rules in terms of names and where they should be stored. With images for example, we must have an image for each density and these should be store in their corresponding density folder. For more information about resources see [User Interface and Visuals](#user-interface-and-visuals) section

## Application Architecture

**Use the architecture that better fits to your project**. Google has released a sample [GitHub](https://github.com/googlesamples/android-architecture) with a collection of samples that demonstrates a list of recommended architectures for Android apps.

The architecture that best fits most of our projects in Beeva has been **MVP + clean architecture**, which stands on the principles of [clean architecture](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html) and is explained in the following diagram.

![MVP + Clean Architecture](https://github.com/googlesamples/android-architecture/wiki/images/mvp-clean.png)

- **MVP**: Model View Presenter pattern.
- **Domain**: Holds all business logic. The domain layer starts with classes named *use cases* or *interactors* used by the application presenters. These use cases represent all the **possible actions** a developer can perform from the presentation layer.
- **Repository**: Repository pattern to provide a local, memory and remote data sources, allowing to use objects without having to know how the objects are persisted.

### Quality

Quality must be mantained in every component of the application, in every class or method. The use of an architecture is a necessary requirement to maintain the quality of a project, but is not sufficient. The [**SOLID principles**](https://es.wikipedia.org/wiki/SOLID) are a good guidelines that can be applied in order to create a system that is easy to maintain and extend over time.

## User Interface and Visuals

Here we will show you the best ways and benefits of structuring a layout file following simple guidelines.

### Activity and fragment uses.

There is currently no pattern indicating when it is best to use a fragment or an activity.

A good practice is the use of fragments to be able to isolate the functionality of each screen and even when creating interface pieces that need to be reused on other screens. In order to leave the activities as light as possible.

On the other hand, when a fragment is destroyed, be sure to delete all references and callbacks to the activity, as well as object references within the activity

Also avoid using nested fragments extensively, use them only when it makes sense.

### Naming
One of the clearest conventions for naming files would be the following. `type_foo_bar.xml`. Examples: `fragment_order_details.xml`, `view_common_product.xml`, `activity_client_profile.xml`.

### Resources in xml files
One of the characteristics that Android has, is the ability to have separate files with all `colors`, `dimensions`, `strings`,etc. Accessible to them from any layout only with the reference. This way we can keep our code better prepared for future changes.</br>

Now a few tips to follow with different resources.

#### Colors

In this file we should only map different colors as if it were a palette. Never use definitions related to the state of a button or a textview because we could start repeating colors very easily.

*Don't do this:*

```xml
<resources>
    <color name="button_border">#FF4081</color>
    <color name="button_background">#3F51B5</color>
    <color name="button_shadow">#272727</color>
    ...
```

*Instead, do this:*

```xml
<resources>
    <color name="pink">#FF4081</color>
    <color name="blue_dark">#3F51B5</color>
    <color name="gray_dark">#272727</color>
    ...
```

#### Dimensions

The same way that we work with the colors file, we should use this as a palette of dimensions

```xml
<resources>
    <!-- TextView paddings -->
    <dimen name="textview_padding_left">15dp</dimen>
    <dimen name="textview_padding_top">10dp</dimen>
    ...

    <!-- font sizes -->
    <dimen name="font_large">18sp</dimen>
    <dimen name="font_normal">15sp</dimen>
    ...
```

#### Strings

Try to be as concrete as possible and generate expats of names for different values.
Unlike other files you can repeat values for more than one key.

**Bad**
```xml
<resources>
    <string name="user_not_found">User not found</string>
    <string name="user_duplicate">User exists</string>
  ...
```

**Good**
```xml
<resources>
  <!-- User's error types -->
    <string name="error_user_not_found">User not found</string>
    <string name="error_user_duplicate">User exists</string>
    ...
```

Note that none of these values must be capitalized. Only the first letter should be capitalized, since we can take advantage of `android:textAllCaps` in case we would like to capitalize everything.

### Use styles to avoid duplicate attributes in layout XMLs.
Properties that are common for different elements or can be repeated frequently in a view, should be externalized in the styles file.

```xml
<style name="button_share">
    <item name="android:background">@null</item>
    <item name="android:textAllCaps">false</item>
    <item name="android:drawablePadding">@dimen/padding_share</item>
    <item name="android:textSize">@dimen/font_small</item>
</style>
```
Applied to Button:
```xml
<Button
    android:id="@+id/btnFacebook"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:drawableTop="@drawable/ic_facebook"
    android:text="@string/facebook"
    style="@style/button_share"
    />
```
### Split a large style file into other files

In the case that we have a very large file with many styles or just because we feel the need to split the styles in any order, we can generate as many styles files as we want.
For example: `styles.xml`, `styles_button.xml`, `styles_label.xml`, `styles_social_media.xml`,

This technique is valid for the different resource files.

[More info] (http://developer.android.com/intl/es/training/best-ui.html).

## Accessibility
This is one of the important parts in our application, if we want it to be used **by everyone**. And we don't mean people from other countries, but **disabled people**.

It's very important that our interface's components contain descriptive labels with the action they perform. In this way, screen readers such as _Talkback_ or _VoiceAssistant_ for Samsung can explain a specific functionality or the content of a screen.

We can define the labels in layout's components in two ways.

_The common way is labelling in the XML file._
```xml
<ImageView
    android:id="@+id/cardImageView"
    android:layout_width="54dp"
    android:layout_height="30dp"
    android:contentDescription="@string/description_default_card"
    android:src="@drawable/default_card"
    />
```

_The dynamic way is setting this attribute in your code._
```kotlin
val resId = if (isPasswordVisible) R.string.hide_password else R.string.show_password
imageButton.contentDescription = resources.getString(resId)
```

Sometimes we want to put an image and we don't want it to be accessible because it's a decorative component in our interface.

_This solves our problem._
```xml
android:importantForAccessibility="no" <!-- For API 16 or higher -->
android:contentDescription="@null" <!-- For API lower than 16 -->
```

### Custom components behaviour
Sometimes we need to make our own custom views. It's very important to keep in mind how we want the screen reader to navigate through our interface.

In the case that we want the screen reader to read all the views of our custom views, we should do it in the following ways.

_Making the root view focusable._

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:focusable="true"
    ...
    >

    <TextView
        android:id="@+id/titleTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        ...
        />

    <TextView
        android:id="@+id/descriptionTextView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        ...
        />
</LinearLayout>
```

The `LinearLayout` will be the view that contains the focus and the screen reader will be able to read the texts of the two `TextView` that it contains. Therefore for the user, this component will be treated as a single component, and the screen reader **won't navigate** through the three views that exist in this layout.

## Coding Style

It is recommended to follow a common style to write code into a team, to publish a public repository, to contribute to one of them, or even for a personal project. This styles are a good rule to maintain the code legibility. Below you can find the code styles of the main programming languages.

 - **Java**
    <br/> [Code conventions] (http://www.oracle.com/technetwork/java/codeconventions-150003.pdf)
    <br/> [Java best practices](https://github.com/beeva/beeva-best-practices/tree/master/backend/java)
 - **Kotlin**
    <br/> [Code conventions] (https://kotlinlang.org/docs/reference/coding-conventions.html)
    <br/> [Kotlin best practices] (kotlin/README.md)
 - **Scala**
    <br/> [Scala style guide] (http://docs.scala-lang.org/style/)
    <br/> [Scala best practices] (https://github.com/beeva/beeva-best-practices/tree/master/backend/scala)

## Libraries

 - Use of libraries. Pros and cons of each one.
 - Don’t add the whole Google Play Services library package.

## Testing

Tests are one of the most important parts of the development. By creating and running unit tests against your code, you can easily verify that the logic of individual units is correct. There are two types of unit tests.

- [**Local tests**](https://developer.android.com/training/testing/unit-testing/local-unit-tests.html): unit tests that run on your local machine only. These tests are compiled to **run locally on the Java Virtual Machine** (JVM) to minimize execution time. Use this approach to run unit tests that have **no dependencies on the Android framework** or have dependencies that can be filled by using mock objects with any mock library like Mockito.

- [**Instrumented tests**](https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests.html): unit tests that run on an Android device or emulator. These tests have access to instrumentation information, such as the Context for the app under test. Use this approach to run unit tests that **have Android dependencies** which cannot be easily filled by using mock objects.

## Performance

 - Use the Parcelable class instead of Serializable when passing data in Intents/Bundles.
 - Perform file operations off the UI thread.
<br/> [More info] (http://developer.android.com/intl/es/training/best-performance.html).

## Security & Privacy

How to keep your app's data secure
<br/> [More info] (http://developer.android.com/intl/es/training/best-security.html).

## Background jobs 

How to run jobs in the background to boost your application's performance and minimize its drain on the battery.
<br/> [More info] (http://developer.android.com/intl/es/training/best-background.html).

## Interaction and Engagement

The best interaction patterns for Android.
<br/> [More info] (http://developer.android.com/intl/es/training/best-ux.html).

## Persistence & Storage

Common solutions to store and persist data. Pros and cons of each one.

 - Shared Preferences.
 - Internal Storage.
 - External Storage.
 - SQLite Databases.

## Analytics & Crash Logs

Solutions to gather metrics of usage and crashes.

 - Crashlytics (crashes, Answers).
 - Google Analytics.

## Continuous Integration

Continuous Integration is a practice, which requires discipline, particularly when the development environment contains a significant amount of complexity.  Best practices are methods or techniques that consistently bring superior results. Best practices evolve over time as new technology and techniques are introduced or improvements discovered.

Here we introduce some best practices for continuous integration that could be especially helpful for mobility teams based in Android.

- **A code repository**. All artifacts required to build the project should be placed in a unique repository.

- **Automate the build and deployment**. A single command should have the capability of building the project. 

 With Gradle we can build by executing: 
 *./gradlew build*
 or if we want to build a flavor:
 *./gradlew assembleflavor*

 Within the build the following steps should be included:
	- Unittests.
	- Sonar or any tool for continuous inspection of code quality.
	- Functional tests.
 For these steps, it can do with:
		- Bamboo: every step can be configured as a task or job in Bamboo. In case any step fails, the builds should fail and automatically notify the team.
		- Jenkins: every step can be configured as a stage within the pipeline of Jenkins. In case any step fails, the builds should fail and automatically notify the team.

- **Commit frequently (at least once a day)**. Developers should commit frequently – at least once per day – several times a day is recommended.  By doing so, developers will be able to know the real state and health of the software, will reduce the number of conflicting changes and will detect possible issues sooner.


- **Fast builds (preferred less than 10 minutes)**. Builds should be fast, anything longer than 10 minutes becomes a dysfunction in the process, because people won’t commit as frequently. Large builds can be broken into multiple jobs and executed in parallel. All tests should run automatically to validate that the software behaves as expected but in case that functional tests like Calabash, Appium, etc, take long time to run, then we can have:
	- One automated build process that runs only a basic set of tests.
	- Another automated process that runs all tests (It can be run every night).
	- Trigger additional tests manually.

- **Everyone can see the latest build**. It should be easy to find out whether the build breaks and, if so, who made the relevant change, so the whole team should be able to easily see these results especially when the build fails. Developers should be notified as soon as possible and why it failed, so that they can fix it as quickly as possible. Every build should be posted in Bamboo or Jenkins and also commits since last build, changes, tests results, etc. 


## Deployment & Publishing
 
 - Use ProGuard or DexGuard.
 - Google developer console.

## Version Control

### Version Your App

Versioning is a critical component of your app upgrade and maintenance strategy. Versioning is important because:

   - It gives users information about the app version that is installed on ther devices and the versions available.
   - Determines compatibility and identifies dependencies between different apps.
   - Services may need to check the app version to determine compatibility and establish upgrade/downgrade relationships.  

Android does not use app version information to enforce restrictions on upgrades, downgrades, or compatibility of third-party apps. Instead, you are responsible for enforcing version restrictions within your app or by informing users of the version restrictions and limitations. The Android system does, however, enforce system version compatibility as expressed by the minSdkVersion setting in the build files. This setting allows an app to specify the minimum system API with which it is compatible.  

**NOTE:** For more information about Version your app [Google Versioning](https://developer.android.com/studio/publish/versioning.html)  

### Set Application Version Information

To define the version information for your app, set values for the version settings in the Gradle build files. These values are then merged into your app's manifest file during the build process.  

If your app defines the app version directly in the `<manifest>` element, the version values in the Gradle build file will override the settings in the manifest. Additionally, defining these settings in the Gradle build files allows you to specify different values for different versions of your app. For greater flexibility and to avoid potential overwriting when the manifest is merged, you should remove these attributes from the `<manifest>` element and define your version settings in the Gradle build files instead.  

Two settings are available, and you should always define values for both of them:
   - *versionCode*: An integer used to determine whether one version is more recent than another. Typically, you would release the first version of your app with versionCode set to 1.  

   - *versionName*: A string used as the version number shown to users. 

**NOTE:** To see how to set these values go [here](../project-configuration/README.md#configure-gradle-to-build-variants)

### Versioning your code

After importing your app into Android Studio, use the Android Studio VCS menu options to enable VCS support for the desired version control system, create a repository, import the new files into version control, and perform other version control operations.

### .gitignore – Exclude Files from Git  
There are some files that shouldn’t be added to Git, such as generated code and binary files (executables). Git provides a feature to inform the list of files that should be excluded in version control.  
A gitignore file is a TXT file with the extension ‘.gitignore’ that specifies intentionally untracked files that Git should ignore. You can put this file in the project root folder in Android Studio. This is an example of .gitignore file for Android projects. It’s almost the same for all Android projects.

	# Mac filetypes  
	.DS_Store  

	# built application files  
	*.apk  

	# files for the dex VM  
	*.dex  

	# Java class files  
	*.class  

	# generated files  
	bin/  
	gen/  

	# Local configuration file (sdk path, etc)  
	local.properties  

	# Intellij project files  
	*.iml  
	*.ipr  
	*.iws  
	.idea/  
	out/  

	# anothers  
	.orig  

	#gradle  
	.gradle/  
	build/  
	app/build/  

### Git Flow

The branching model of Git Flow is particularly useful for Android development since it is simpler to debug issues happening on production that might not be happening on development, checking out the version code that has the crashes and debugging from there.   

**NOTE:** For more information about the usefulness of using Git Flow [Git Flow for Android Development](https://riggaroo.co.za/using-git-flow-for-android-development/)

**NOTE:** For more information about Git Flow [DevOps Repository](https://github.com/beeva/beeva-best-practices/tree/master/devops/git#git-flow)








