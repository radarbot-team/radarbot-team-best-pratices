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

For more information about `String` [_format specifiers_](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html).

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

### Third party libraries

Although `print` and `NSLog` are very useful options and it is very easy to use them, they are quite simply. Moreover, these options only write in console or ASL but they do not save a log in a file then we are not able to send to us if we need it. For these reasons, there are some third party libraries which are more powerful and more customizable than the Apple ones.

+ #### CocoaLumberjack

##### Benefits
+ Define different log levels.
+ Choose the output: Xcode console, Apple System Logs or/and file.
+ Define **rolling frequency** and **maximum file size**.
+ Faster than NSLog.

+ #### SwiftyBeaver

##### Benefits
+ Define different log levels.
+ Choose the output: Xcode console, file or/and **cloud**.
+ **Colored** output depending on log level.
+ AES256 encryptation to send logs to cloud.
+ Easy configuration for each output.
+ Mac app to analyze logs sent from your app.


+ #### CleanroomLogger

##### Benefits
+ Define different log levels, marking a special tag "log severity".
+ Make easier to find the exact line of code where is printing the log.
+ Has a function `trace()` which shows the exact execution path.
+ Is extensible, it allows to define custom implementations of different functions that could be used during the logging process.
+ Define **rolling frequency**.
+ Choose the output: Xcode console, Apple System Logs or/and file.


## Bibliography

**Articles**

+  Debasis Das (March 2015). [_Swift print, println, NSLog_](http://www.knowstack.com/swift-print-println-nslog/)

**Github**

+ [_CocoaLumberjack_](https://github.com/CocoaLumberjack/CocoaLumberjack)
+ [_SwiftyBeaver_](https://github.com/SwiftyBeaver/SwiftyBeaver)
+ [_CleanroomLogger_](https://github.com/emaloney/CleanroomLogger)
