# iOS best practices
<!-- MarkdownTOC depth=0 autolink=true bracket=round -->

- [Basis](#basis)
- [Project Configuration](#project-configuration)
- [Application Architecture](#application-architecture)
- [Design Patterns](#design-patterns)
- [User Interface and Visuals](#user-interface-and-visuals)
	- [Animations](#animations)
	- [Image Assets](#image-assets)
- [Coding Style](#coding-style)
	- [Objective-C](#objective-c)
	- [Swift](#swift)
- [i18n & l10n](#i18n--l10n)
- [Persistence & Storage](#persistence--storage)
	- [Secure Storage](#secure-storage)
- [Networking](#networking)
- [Concurrency](#concurrency)
- [Logging](#logging)
- [Debugging](#debugging)
- [Testing](#testing)
	- [Unit testing](#unit-testing)
	- [UI Testing](#ui-testing)
- [Frameworks](#frameworks)
- [Libraries & Dependencies Management](#libraries--dependencies-management)
- [Analytics & Crash Logs](#analytics--crash-logs)
- [Diagnostics & Profiling](#diagnostics--profiling)
- [Continuous Integration](#continuous-integration)
- [Deployment & Publishing](#deployment--publishing)
- [Version Control](#version-control)

<!-- /MarkdownTOC -->

## Basis

1. Getting Started: official documentation and guides.

2. How to install and update Xcode.

3. Human Interfaces Guidelines and design topics.

## Project Configuration

1. Universal applications

2. Schemes

3. Targets

4. Configurations

5. Warnings configuration

6. Build Settings and project info.plist

7. Xcode build phases and run scripts

8. Structure of folders and groups

## Application Architecture

Common application architectures. Pros and cons of each one.

1. Apple’s official Model-View-Controller. Avoiding *massive view controller*.

2. Alternatives: MVVM, MVP, VIPER

## Design Patterns

Common patterns on iOS, how to implement them and recommendations when using them.

1. Initialization, chain of initializers

2. Delegation

3. Target-Action

4. Observation (KVO, NSNotificationCenter)

5. Factory (Class clusters)

6. Prototype (copy, copyWithZone)

7. Command (NSInvocation)

8. Extensions (Objective-C categories, Swift extensions)

9. Dependency injection

## User Interface and Visuals

How to declare an user interface.

1. AutoLayout

2. Programmatically

3. Using Interface Builder

### Animations

Animating views with:

1. Core Animation

### Image Assets

1. Image format

2. Image size

3. Asset catalog

4. App icon

5. Launch image

## Coding Style

Best practices and style when writing code in the official languages.

### Objective-C 

[Objective-C best practices](objective-c/README.md)

Some examples [here](https://github.com/github/objective-c-style-guide) and [here](https://github.com/raywenderlich/objective-c-style-guide).

### Swift

[Swift best practices](swift/README.md)

Some examples [here](https://github.com/github/swift-style-guide) and [here](https://github.com/raywenderlich/swift-style-guide).

## i18n & l10n

Process of internationalization and localization of an iOS app. 

1. Localization

    1. Localizable.strings files

    2. Existing tools (i.e. Twine) used to improve the process of localization.

2. Internationalization

    3. NSLocale

    4. NSDateFormatter

    5. NSNumberFormatter

## Persistence & Storage

Common solutions to store and persist data.

1. Plist files

2. Archive and unarchiving

3. Documents folder (NSFileManager)

4. NSUserDefaults

5. Core Data & SQLite

6. iCloud

7. Third party libraries (Realm).

### Secure Storage

Sensitive information and its storage and treatment.

1. Keychain

## Networking

1. Handling URLs (NSURLComponents)

2. SSL & TLS

    1. SSL pinning

3. Downloading and parsing data

4. Third party libraries (AFNetworking, Alamofire, SwiftJSON, AEXML, …)

## Concurrency

Concurrency on iOS. Pros and cons of each technology.

1. NSThread

2. Grand Central Dispatch

3. NSOperation and NSOperationQueue

## Logging

Writing to Xcode console. Logging solutions.

1. NSLog

2. Swift’s print

3. Third party libraries (CocoaLumberjack)

## Debugging

1. Tips and tricks when debugging a program

    1. Console and inspector

    2. Debugging web content (Safari inspector)

    3. UI (Xcode view debugging)

    4. iOS Network link conditioner

2. Third party tools (Reveal, Spark Inspector, …)

## Testing

### Unit testing

1. XCTest

2. Third party libraries (Nimble, ...)

### UI Testing

1. Xcode UI tests

2. Recording tests

3. Third party libraries (KIF, Calabash, …)

## Frameworks

Programming a framework.

1. Generating the framework artifact

2. Cross platform frameworks for iOS, watchOS and tvOS

## Libraries & Dependencies Management

Solutions of third party libraries dependency management.

1. Cocoapods

2. Carthage

3. Swift Package Manager (Swift 3)

4. Git submodules

## Analytics & Crash Logs

Solutions to gather metrics of usage and crashes.

1. itunesconnect

2. Crashlytics (crashes, Answers)

3. Google Analytics

## Diagnostics & Profiling

1. Xcode static analyzer

2. Xcode Instruments

3. Third party solutions (swiftlint, Faux Pax, ObjectiveClean, SwiftClean, …)

## Continuous Integration

Solutions of CI that best matches the lifecycle of an iOS application.

1. Xcode Server and Bots

2. Command line builds

3. Fastlane

4. Bamboo, Jenkins, Travis CI, BuddyBuild, ...

## Deployment & Publishing

1. Code signing

2. Provisioning profiles

3. Upload to the App Store

4. App Store Review Guidelines

## Version Control

1. Git and the .gitignore file.

2. Tips & tricks while merging complex XML files like Storyboards, XIB and the .xcodeproj file.

