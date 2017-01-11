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

- Getting Started: official documentation and guides.
- How to install and update Android Studio, JDK, SDK.

## Project Configuration

- Configure gradle to build variants, target sdk, min sdk…
- Structure of folders and packages.

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
    <string name="error.user.not_found">User not found</string>
    <string name="error.user.duplicate">User exists</string>
    ...
```

Note that none of these values must be capitalized. Only the first letter should be capitalized, since we can take advantage of `android:textAllCaps` in case we would like to capitalize everything.

### Use styles to avoid duplicate attributes in layout XMLs.
Properties that are common for different elements or can be repeated frequently in a view, should be externalized in the styles file.

```xml
<style name="Button.Share">
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
    style="@style/Button.Share"
    />
```
### Split a large style file into other files

In the case that we have a very large file with many styles or just because we feel the need to split the styles in any order, we can generate as many styles files as we want.
For example: `styles.xml`, `styles_button.xml`, `styles_label.xml`, `styles_social_media.xml`,

This technique is valid for the different resource files.

[More info] (http://developer.android.com/intl/es/training/best-ui.html).

## Coding Style

 - Java
    <br/> [code conventions] (http://www.oracle.com/technetwork/java/codeconventions-150003.pdf)
    <br/> [Java Best Practices](https://github.com/beeva/beeva-best-practices/tree/master/backend/java) 
 - Kotlin
    <br/> [coding conventions] (https://kotlinlang.org/docs/reference/coding-conventions.html)
    <br/> [Kotlin Best Practices] (kotlin/README.md)
 - Scala
    <br/> [Scala Style Guide] (http://docs.scala-lang.org/style/)
    <br/> [Scala Best Practices] (https://github.com/beeva/beeva-best-practices/tree/master/backend/scala)

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

 - Use of Gradle.
 - Bamboo, Jenkins..

## Deployment & Publishing
 
 - Use ProGuard or DexGuard.
 - Google developer console.

## Version Control

Git and the .gitignore file.








