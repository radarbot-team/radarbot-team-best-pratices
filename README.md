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

Git and the .gitignore file.








