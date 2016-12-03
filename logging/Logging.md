# Logging

Logging is very useful in all languages to allow debug applications and to help to solve bugs. In iOS development, we can use `NSLog` for logging apps written on Objective-C and `print` also in Swift. Basically, these solutions write a line of text in console. If we wanted to send logs of our iOS app, we would have to save them in a file first, for this purpose we can use a third party library as `CocoaLumberjack`.

### NSLog

Use `NSLog` for debugging is very easy, it is just like a create a `NSString`. It has the following declaration:

```Objective-C
void NSLog(NSString *format, ...);
```

The main characteristics of `NSLog` are:

+ `NSLog` is slow.
+ It is synchronous.
+ It outputs log to ASL and Xcode console.
+ It has not log levels what makes more difficult to tell the difference between errors, warnings, and other information.
+ It only works when the device is attached to the computer, then it is not a solution for remote access.

Examples of using:

```Objective-C

NSLog(@"Hello world"); // Writes in console: Hello world

NSLog(@"Hello world %@", @"Tim"); // Writes in console: Hello world Tim

NSLog(@"Tim is %i years old", 43); // Writes in console: Tim is 43 years old

```

For more information about `String` format specifiers: https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html

##### Logging an object

In many cases, it would be useful to print in console a complete object. If we try to print it directly, we will show the name of the class and its memory address in console.

Example:

```Objective-C

// Header file
@interface Person: NSObject

@property (nonatomic, copy, readonly) NSString *name;
@property (nonatomic, copy, readonly) NSString *surname;

- (id)initWithName:(NSString *)aName andSurname:(NSString *)aSurname;

@end

// Implementation file

#import "Person.h"

@implementation Person

- (id)initWithName:(NSString *)aName andSurname:(NSString *)aSurname
{
    if (self = [super init]) {

        _name = aName;
        _surname = aSurname;

    }

    return self;
}

@end

// Using NSLog

Person *person = [[Person alloc] initWithName:@"Tim" andSurname:@"Cook"];

NSLog(@"%@", person); // Writes in console: <Person: 0x137641b00>

```

If we want to show a better log, we can override the property `description` with a proper string.

```Objective-C

// Header file
@interface Person: NSObject

@property (nonatomic, copy, readonly) NSString *name;
@property (nonatomic, copy, readonly) NSString *surname;

- (id)initWithName:(NSString *)aName andSurname:(NSString *)aSurname;

@end

// Implementation file

#import "Person.h"

@implementation Person

- (id)initWithName:(NSString *)aName andSurname:(NSString *)aSurname
{
    if (self = [super init]) {

        _name = aName;
        _surname = aSurname;

    }

    return self;
}

- (NSString*)description
{
    return [NSString stringWithFormat:@"Name: %@, Surname: %@", self.name, self.surname];
}

@end

// Using NSLog

Person *person = [[Person alloc] initWithName:@"Tim" andSurname:@"Cook"];

NSLog(@"%@", person); // Writes in console: Name: Tim, Surname: Cook

```



#### Using DLog instead of NSLog

A good practice is not to show logs in your release version, in some cases can be useful but most of them should not be printed. Defining a macro `DLog` in the `.pch` file and using instead of `NSLog`, it will allow you to print logs only in debug mode but it will never print them on release mode.

Macro:

```Objective-C

#ifndef DLog
#ifdef DEBUG
#define DLog(_format_, ...) NSLog(_format_, ## __VA_ARGS__)
#else
#define DLog(_format_, ...)
#endif

```

### Swift’s print

We can use `NSLog` in Swift also, but it is a better practice to use `print`. Basically, print will add a newline at the end of its content as `NSLog`.

##### NSLog vs print


+ `NSLog` is slower.

+ `NSLog` adds a timestamp and identifier to the output, whereas print will not.

+ `NSLog` synchronizes the log statements so that if you’re issuing logs from different threads concurrently, they won’t overlap with each other; print can result in jumbled output if performed simultaneously from separate threads without doing some synchronization (e.g. dispatching it to some serial queue, such as the main queue).

+ When performed on physical device, `NSLog` statements appear in the device’s console whereas print only appears in the debugger console.

Examples of using:

```Swift

let name = "Tim"
let age = 43

print("Hello world"); // Writes in console: Hello world

print("Hello world \(name)"); // Writes in console: Hello world Tim

print("Tim is \(age) years old"); // Writes in console: Tim is 43 years old

```

##### Logging an object

In many cases, it would be useful to write in console a complete object. If we try to print it directly, we will show the name of the class in console.

Example:

```Swift

class Person {

    var name: String
    var surname: String

    init(name: String, surname: String) {
        self.name = name
        self.surname = surname
    }

}

print(person) // Writes in console: Person

```

If the class implement the protocol `CustomStringConvertible` for other purposes, we will this string in console.

Example:

```Swift

class Person {

    var name: String
    var surname: String

    init(name: String, surname: String) {
        self.name = name
        self.surname = surname
    }

}

extension Person: CustomStringConvertible {
    var description: String {
        return "Name: \(name) - Surname: \(surname)"
    }
}


print(person) // Writes in console: Name: Tim - Surname: Cook

```

But if we do not want to implement this protocol but you want to have a better description of the object in console, you can implement `CustomDebugStringConvertible` protocol.

Example:

```Swift

class Person {

    var name: String
    var surname: String

    init(name: String, surname: String) {
        self.name = name
        self.surname = surname
    }

}

extension Person: CustomDebugStringConvertible {
    var debugDescription: String {
        return "Debugging --> Name: \(name) - Surname: \(surname)"
    }
}


print(person) // Writes in console: Debugging --> Name: Tim - Surname: Cook

```

### Third party libraries (CocoaLumberjack)

Although `print` and `NSLog` are very useful options and it is very easy to use them, they are quite simply. Moreover, these options only write in console but they do not save a log in a file then we are not able to send to us if we need it. If we need a log system more complex or/and if we need to save a log in a file to be able to send them to our server or by email, `CocoaLumberjack` is the best option open source. `CocoaLumberjack is a fast & simple, yet powerful & flexible logging framework for Mac and iOS.`

#### Benefits

+ Define different log levels.
+ Choose the output: Xcode console, Apple System Logs, disk, etc.
+ Define rolling frequency and maximum file size.

#### Requirements

The current version of Lumberjack requires:

+ Xcode 8 or later
+ Swift 3.0 or later
+ iOS 5 or later
+ OS X 10.7 or later
+ WatchOS 2 or later
+ TVOS 9 or later

Backwards compability

+ for Xcode 7.3 and Swift 2.3, use the 2.4.0 version
+ for Xcode 7.3 and Swift 2.2, use the 2.3.0 version
+ for Xcode 7.2 and 7.1, use the 2.2.0 version
+ for Xcode 7.0 or earlier, use the 2.1.0 version
+ for Xcode 6 or earlier, use the 2.0.x version
+ for OS X < 10.7 support, use the 1.6.0 version

#### 1. Installation

##### CocoaPods

You just added in your pod file and install.

+ Swift

```Swift

platform :ios, '8.0'

target 'SampleTarget' do
  use_frameworks!
  pod 'CocoaLumberjack/Swift'
end

```

+ Objective-C

```Objective-C

platform :ios, '7.0'
pod 'CocoaLumberjack'

```

##### Carthage

To install with Carthage, follow the instruction on [Carthage](https://github.com/Carthage/Carthage).

Cartfile

```Objective-C
github "CocoaLumberjack/CocoaLumberjack"
```

#### 2. First steps

##### Adding System Loggers

Add the following code in Swift or Objective-C in the method `application:didFinishLaunchingWithOptions:` of `AppDelegate`.

+ Swift

```Swift
DDLog.add(DDTTYLogger.sharedInstance()) // TTY = Xcode console
DDLog.add(DDASLLogger.sharedInstance()) // ASL = Apple System Logs

```

+ Objective-C

```Objective-C
[DDLog addLogger:[DDTTYLogger sharedInstance]]; // TTY = Xcode console
[DDLog addLogger:[DDASLLogger sharedInstance]]; // ASL = Apple System Logs

```

##### Adding File Logger

Add the following code in Swift or Objective-C in the method `application:didFinishLaunchingWithOptions:` of `AppDelegate`.

+ Swift


```Swift

let fileLogger: DDFileLogger = DDFileLogger() // File Logger
fileLogger.maximumFileSize = 1024 * 1024; // 1MB
fileLogger.rollingFrequency = TimeInterval(60*60*24)  // 24 hours
fileLogger.logFileManager.maximumNumberOfLogFiles = 7
DDLog.add(fileLogger)

```

+ Objective-C

```Objective-C
DDFileLogger *fileLogger = [[DDFileLogger alloc] init]; // File Logger
fileLogger.maximumFileSize = 1024 * 1024; // 1MB
fileLogger.rollingFrequency = 60 * 60 * 24; // 24 hour rolling
fileLogger.logFileManager.maximumNumberOfLogFiles = 7;
[DDLog addLogger:fileLogger];

```
This configuration defines 7 as a number maximum of log files, if there are 7 files already the older will be override. The size maximum of each file will be 1 MB and each 24 hours will start a new file, but if the size of the current file achieves 1 Mb it will be started a new one also.


##### Defining log level

It would be a good practice to have a different log level depending on the version of the app. If we are developing, it could be a good idea to have all kind of logs then we can set the level in Verbose mode. But if we are in the release version could be better to have only important logs as warnings and errors. For this configuration we can use the next code:

```Objective-C
#ifdef DEBUG
  static const DDLogLevel ddLogLevel = DDLogLevelVerbose;
#else
  static const DDLogLevel ddLogLevel = DDLogLevelWarning;
#endif
```

##### Writing logs

Finally we have to use `CocoaLumberjack` log "functions" to write log lines instead of NSLog and print. We can use the following levels:

+ Swift

```Swift
DDLogVerbose("Verbose");
DDLogDebug("Debug");
DDLogInfo("Info");
DDLogWarn("Warn");
DDLogError("Error");
```

+ Objective-C

```Objective-C
DDLogVerbose(@"Verbose");
DDLogDebug(@"Debug");
DDLogInfo(@"Info");
DDLogWarn(@"Warn");
DDLogError(@"Error");
```

## Bibliography

**Articles**

+  Debasis Das (March 2015). [_Swift print, println, NSLog_](http://www.knowstack.com/swift-print-println-nslog/)

+ Bart Jacobs (March 2013). [_CocoaLumberjack: Logging on Steroids_](http://blog.scottlogic.com/2015/02/11/swift-kvo-alternatives.html)

**Github**

+ [_CocoaLumberjack_](https://github.com/CocoaLumberjack/CocoaLumberjack)
