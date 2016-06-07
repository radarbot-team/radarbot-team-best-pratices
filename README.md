# Android best practices
<!-- MarkdownTOC depth=0 autolink=true bracket=round --> 

 This is how we work with Android at BEEVA.

<img  style="float:left"; src="statics/horizontal-beeva-logo.png" />  
<img  src="statics/logo-android.jpg" /> 


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
- [Interaction and Engagement](#interaction-engagement)
- [Persistence & Storage](#persistence--storage)
- [Analytics & Crash Logs](#analytics--crash-logs)
- [Continuous Integration](#continuos-integration)
- [Deployment & Publishing](#deploymnet--publising)
- [Version Control](#version-control)

<!-- /MarkdownTOC -->

## Basis

- Getting Started: official documentation and guides.
- How to install and update Android Studio, JDK, SDK.

## Project Configuration

- Configure gradle to build variants, target sdk, min sdk…
- Structure of folders and packages.

## Application Architecture

Common application architectures. Pros and cons of each one.

 - Model View Presenter. [Ejemplos de Google](https://github.com/googlesamples/android-architecture)
 - Model View ViewModel.
 - Others.

## User Interface and Visuals

How to build a user interface using Android layouts for all types of devices.<br/> 

 - Activity and fragment uses.
 - Use styles to avoid duplicate attributes in layout XMLs.
 - Add resources in xml files (color,string,dimen…).
 - include resources for all dimens.
<br/> [More info] (http://developer.android.com/intl/es/training/best-ui.html).
<br/> Material design: best practices (components, colors,  animations…).

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

 - Unit testing
 - UI testing 
 <br/> [More info] (http://developer.android.com/intl/es/training/testing/index.html).

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








