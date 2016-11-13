# Design Patterns

## Object Initialization

Initialization sets the instance variables of an object to reasonable and useful initial values. It can also allocate and prepare other global resources needed by the object. Every object that declares instance variables should implement an initializing method—unless the default set-everything-to-zero initialization is sufficient. If an object does not implement an initializer, Cocoa invokes the initializer of the nearest ancestor instead.

Cocoa allows declare multiple initializers, with one or more parameters, which are instance methods that begin with **init** and return an object of the dynamic type *id* in Objective-C, and an object of specific class in Swift.

Here are a few examples of initializers:

**Objective-C** 

	- (instanceType)initWithFrame:(NSRect)frameRect;
	- (instancetype)initWithTimeInterval:(NSTimeInterval)secsToBeAdded sinceDate:(NSDate *)date;

**Swift**

	init(frame: CGRect)
	init(timeInterval secsToBeAdded: TimeInterval, since date: Date)
	
### Designated and Convenience initializers

The designated initializer plays an important role for a class. It ensures that inherited instance variables are initialized by invoking the designated initializer of the superclass. It is typically the init...` method that has the most parameters and that does most of the initialization work, and it is the initializer that secondary initializers of the class invoke with messages to self.

The designated initializer pattern helps ensure that inherited initializers properly initialize all instance variables. A subclass that needs to perform nontrivial initialization should override all of its superclass’s designated initializers, but it does not need to override the convenience initializers.

In **Objective-C**, object initialization is based on the notion of a *designated initializer*, an initializer method that is responsible for calling one of its superclass’s initializers and then initializing its own instance variables. Initializers that are not designated initializers are known as *convenience initializers*. Convenience initializers typically delegate to another initializer—eventually terminating the chain at a designated initializer—rather than performing initialization themselves.

To clarify the distinction between designated and designated initializers clear, you can add the `NS_DESIGNATED_INITIALIZER` macro to any method in the init family, denoting it a designated initializer. Using this macro introduces a few restrictions:

* The implementation of a designated initializer must chain to a superclass init method (with `[super init...]`) that is a designated initializer for the superclass.
* The implementation of a convenience initializer (an initializer not marked as a designated initializer within a class that has at least one initializer marked as a designated initializer) must delegate to another initializer (with [self init...]).
* If a class provides one or more designated initializers, it must implement all of the designated initializers of its superclass.

If any of these restrictions are violated, you receive warnings from the compiler.

If you use the `NS_DESIGNATED_INITIALIZER` macro in your class, you need to mark all of your designated initializers with this macro. All other initializers will be considered to be convenience initializers.

Example:

	- (instancetype)initWithName:(NSString *)name NS_DESIGNATED_INITIALIZER;
	- (instancetype)init;


The figure below shows the overall initializer chain for all three classes:

![Objective-C Initialization chain](statics/ObjectiveC.InitializationChain.png)

In **Swift**, *designated initializers* are the primary initializers for a class. A designated initializer fully initializes all properties introduced by that class and calls an appropriate superclass initializer to continue the initialization process up the superclass chain.

Classes tend to have very few designated initializers, and it is quite common for a class to have only one. Designated initializers are “funnel” points through which initialization takes place, and through which the initialization process continues up the superclass chain.

Every class must have at least one designated initializer. In some cases, this requirement is satisfied by inheriting one or more designated initializers from a superclass.

*Convenience initializers* are secondary, supporting initializers for a class. You can define a convenience initializer to call a designated initializer from the same class as the convenience initializer with some of the designated initializer’s parameters set to default values. You can also define a convenience initializer to create an instance of that class for a specific use case or input value type.

You do not have to provide convenience initializers if your class does not require them. Create convenience initializers whenever a shortcut to a common initialization pattern will save time or make initialization of the class clearer in intent.

There is a private word to use when an initializer is a convenience one, and this word is `convenience`. Designated initializers do not use any reserved word to denote it.

Example:

```	
	init(name: String) {
		...
	}
	
	convenience init() {
		self.init(name: "The name")
	} 
```
In Swift, the initializer chain looks like the figure below:

![Swift Initialization chain](statics/Swift.InitializationChain.png)


## Delegation

**Delegation** is a pattern in which one object in a program acts on behalf of, or in coordination with, another object. The delegating object keeps a reference to the other object `the delegate` and at the appropiate time sends a message to it. The message informs the delegate of an event that the delegating object is about to handle or has just handled. The delegate may respond to the message by updating the appearance or state of itself or other objects in the application, and in some cases it can return a value that affects how an impending event is handled. Delegation makes it possible for one object to after the behaviour of another object without the need to inherit from it. The delegate is almost always one of your custom objects, an by definition it incorporates application-specific logic that the generic and delegating object cannot possibly know itself.

### How does it work?

The delegating class has a property, usually named `delegate`, and declares, without implementing, one or more methods that constitute a *formal protocol* or an *informal protocol*.

>* A **formal protocol** declares a list of methods that client classes are expected to implement. Formal protocols have their own declaration, adoption, and type-checking syntax. You can designate methods whose implementation is required or optional. 

>* An **informal protocol** is a category on NSObject, which implicitly makes almost all objects adopters of the protocol. Implementation of the methods in an informal protocol is optional. Before invoking a method, the calling object checks to see whether the target object implements it. A clear example of informal protocol, is UIapplicationDelegate. Its methods are implemented by AppDelegate class of the application.

> To find more infomation about *Protocol*, [click here](https://developer.apple.com/library/content/documentation/General/Conceptual/DevPedia-CocoaCore/Protocol.html)

In the informal protocol approach, the delegate implements only those methods in wich it has an interest in coordinating itself with the delegating object or affecting that object's default behaviour. If the delegating class declares a formal protocol, the delegate may choose to implement those methods marked optional, but it must implement the required ones.

The mechanism of delegation is illustrated by the next figure:

![Delegation mechanism](statics/MechanismDelegation.png)

### The form of delegation messages

Delegating methods have a conventional form. They begin with the name of the class object doing the delegating. Usually this object name is followeb by an auxiliary verb indicative of the tempral status of the reported event. This verb, in other words, indicates wheter the event is about to occur (*Should* or *Will*) or whether it has just occurred (*Did* or *Has*). This temporal distinction helps to categorize those messages that expect a return value and those that do not. 

Delegation methods with return values

```
	NSApplication
	- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename; 
	
	UIApplicationDelegate
	- (BOOL)application:(UIApplication *)application handleOpenURL:(NSURL *)url;

	UITableViewDelegate
	- (UITableRowIndexSet *)tableView:(NSTableView *)tableView willSelectRows:(UITableRowIndexSet *)selection;

	NSWindow
	- (NSRect)windowWillUseStandardFrame:(NSWindow *)window defaultFrame:(NSRect)newFrame;
```

The delegate that implements these methods can block the impending event (by returning *NO* in the first two methods), or alter a suggested value (the index set and the frame rectangle in the last two methods).

Other delegation methods are invoked by messages that do not expect a return value and so are typed to return *void*. These messages are purely informational, and the method names often contain *Did*, *Will*, or some other indication of a transpired or impending event.

```
	NSWindow 
	- (void)windowDidMove:(NSNotification *)notification; 

	UIApplication	
	- (void)application:(UIApplication *)application willChangeStatusBarFrame:(CGRect)newStatusBarFrame;

	NSApplication
	- (void)applicationWillBecomeActive:(NSNotification *)notification;
```

## Target-Action

A typical application's user interface consists of a number of graphical objects, and perhaps the most common of these objects are controls. A control is a graphical analog of a real-world or logical device (button, switch, textfield...). As with real-world control, such as radio tuner, you use it to convey your intent to some system of which it is a part-that is, an application.

The role of a control on a user interface is simple: it interprets the intent of the user and instructs some other objects to carry out that request. When a user acts on the control by, say, clicking it or pressing the *Return key*, the hardware device generates a raw event. The control accepts the event and translates it into an instruction that is specific to the application. However, events by themselves do not give much information about the user's intent, they marely tell you that the user clicked a mouse button or pressed a key. So some mechanism must be called upon to provide the translation between event and instruction. This mechanism is called **target-action**.

Cocoa uses the target-action mechanism for communication between a control and another object. This mechanism allows the control and to encapsulate the information necessary to send an application-specific instruction to the appropiate object. The receiving object is called the `target`. The `action` is the message that the control sends to the target. The object that is interested in the user event `-the target-`is the one that imparts significance to it, and this significance is usually reflected in the name it gives to the action.

![Target-Action mechanism](statics/TargetAction.png)

### The target

A target is a receiver of an action message. A control holds the target of its action message as an outlet. The target usually is an instance of one of yout custom classes, although it can be any Cocoa object whose class implements the appropiate action method.

Control objects do not retain their targets. However, clients of controls sending action messages are responsible for ensuring that their targets are available to receive action messages. To do this, they may have to retain their targets in memory-managed environments. 

### The action

An action is the message a control sends to the target or, from the perspective of the target, the method that the target implements to respond to the action message. A control stores an action as an instance variable of type `SEL -Objective-C-` or `Selector -Swift-`, -data types used to specify the signature of a message-. An action message must have a simple, distinct signature. The method it invokes returns nothing and usually has a parameter containing the object sending the message. This parameter, by convention, is named *sender*.

Example of action methods with one, two or three parameters:

```
	Objective-C
	
	- (void)doSomething;
	- (void)doSomething:(id)sender;
	- (void)doSomething:(id)sender forEvent(UIEvent *)event;

	Swift
	
	func doSomething()
	func doSomething(sender: UIButton)
	func doSomething(sender: UIButton forEvent event: UIEvent)
```

Action methods declared by some Cocoa classes can also have the equivalent signature:

```
	Objective-C
	
	- (IBAction)doSomething:(id)sender;
	- (IBAction)doSomething:(id)sender forEvent(UIEvent *)event;

	Swift
	
	@IBAction func doSomething(sender: UIButton)
	@IBAction func doSomething(sender: UIButton forEvent event: UIEvent)
```

In this case, `IBAction` does not designate a data type for a return value, no value is returned. *IBAction* is a type qualifier that Interface Builder notices during application development to synchronize actions added programmatically with its internal list of action methods defined for a project.

The sender parameter usually identifies the control sending the action message. The target can query the sender for more information if it needs to. If the actual sending substitutes another object as sender, you should treat that object in the same way. 

## Observation 

### Key-Value Observing (KVO)

Key-value Observing, `<NSKeyValueObserving>, or KVO`, is an informal protocol that defines a common mechanism that allows objects to be notified of changes to specified properties of other objects. As an informal protocol, classes do not conform to it, it's just implicitly assumed for all subclasses of `NSObject`.

KVO's primary benefit is that you do not have to implement your own scheme to send notifications every time a property changes. Its well-defined infrastructure has framework-level support that make it easy to adopt. In addition, the infrastructure is already full-featured, which makes it easy to support multiple observers for a single property, as well as dependent values.

To use KVO, first you must ensure that the observed object is *KVO Compliant*. If your objects inherit from *NSObject* and you create properties in the usual way, your objects and their preoperties will automatically be KVO Compliant. It is also possible to implement compliance manually. [**KVO Compliance**](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/KeyValueObserving/Articles/KVOCompliance.html#//apple_ref/doc/uid/20002178-BAJEAIEE) describes the difference between automatic and manual key-value observing, and how to implement both.

![KVO pattern UML](statics/ObservationKVO.png)

#### Registering as an Observer

An observing object first registers itself with the observed object by sending an `addObserver:forKeyPath:options:context` message, passing itself as the observer and the key path of the property to be obsrved. The observer additionally specifies an options parameter and a context pointer to manage aspects of the notifications.

```
- (void)addObserver:(NSObject *)observer 
         forKeyPath:(NSString *)keyPath 
            options:(NSKeyValueObservingOptions)options 
            context:(void *)context;
```
> * `observer`: The object to register for KVO notifications. The observer must implement the key-value observing method *observeValueForKeyPath:ofObject:change:context:*
> * `keyPath`: The key path, relative to the receiver, of the property to observe. This value must not be *nil*.
> * `options`: A combination of the [NSKeyValueObservingOptions](https://developer.apple.com/reference/foundation/nskeyvalueobservingoptions?language=objc) values that specifies what is included in observation notifications. 
> * `context`: Arbitrary data that is passed to `observer` in *observeValueForKeyPath:ofObject:change:context:*

#### Receiving notification of a change

When the value of an observed property of an object changes, the observer receives an *observeValueForKeyPath:ofObject:change:context:* message. All observers must implement this method.

The observing object provides the key path that triggered the notification, itself as the relevant object, a dictionary containing details about the change, and the context pointer that was provided when the observer was registered for this key path.

A typical implementation of this method looks something like this:

```
- (void)observeValueForKeyPath:(NSString *)keyPath
                      ofObject:(id)object
                        change:(NSDictionary *)change
                       context:(void *)context {
 
    if ([keyPath isEqualToString:@"name"]) {
        //...
 
    } else {
        // Any unrecognized context must belong to super
        [super observeValueForKeyPath:keyPath
                             ofObject:object
                               change:change
                               context:context];
    }
}
```
In any case, the observer should always call the superclass's implementation of *observeValueForKeyPath:ofObject:change:context:* when it does not recognize the context or the key path, because this means a suplerclass has registered for notifications as well.

> **Note**: If a notification propagates to the top of the class hierarchy, *NSObject* throws a *NSInternalInconsistencyException* because this is a programming error: a subclass failed to consume a notification for which is registered.

#### Removing an Object as an Observer

You remove a key-value observer by sending the observed object a *removeObserver:forKeyPath:context:* message, specifiying the observing object, the key path, and the context. 

After receiving a *removeObserver:forKeyPath:context:* message, the observing object will no longer receive any *observeValueForKeyPath:ofObject:change:context:* messages for the specified key path and object.

If you make a call to *removeObserver:forKeyPath:context:* when the object **is not registered* as an observer (whether because it was already unregistered or not registered in the first place), an exception is thrown. The kicker is that *there is no built-in way to even check if an object is registered*. So, the best option is to invoke *removeObserver:forKeyPath:context:* inside `@try @catch` block.

```
@try {
	[object removeObserver:self
			  	  forKeyPath:"name"];
}
@catch (NSException * __unused exception) {}
```

### NSNotificationCenter

An `NSNotificationCenter`object (or simply, **notification center**) provides a mechanism for bradcasting information within a program. An NSNotificationCenter object is essentially a notification dispatch table.

NSNotificationCenter provides a centralized hub through which any part of an application may notifiy and be notified of changes from any other part of the application. Observers register with a notification center to respond to particular events with a specified action. Each time an event occurs, the notification goes through its dispatch table, and messages any registered observers for that event.

Each NSNotification object has a *name*, with additional context optionally provided by an associated *object* and *userInfo* dictionary.

#### Adding observers

The traditional way to add an observer is calling `addObserver:selector:name:object:` of NSNotificationCenter, in which an object, usually `self`, adds itself to have the specified selector performed when a matching notification is posted.

The modern, block-bassed API for adding notification observers is `addObserverForName:object:queue:usingBlock`. Instead of registering an existing object as an observer of a notification, this method creates its own anonymous object to be the observer, which performs a block on the specified queue (or the calling thead, if *nil*) when a matching notification is posted. Unlike its similary named *@selector*-based counterpart, this method actually returns the constructed observer object, which is necessary for unregistering the observer.

The `name` and `object` parameters of both methods are used to decide whether the criteria of a posted notification match the observer. If *name* is set, only notifications with that name will trigger, but if *nil* is set, then *all names* will match. The same is true of *object*. So, if both, *name* and *object* are set, only notifications with that name and the specified object will trigger. However, if both are nil, then all notifications posted will trigger.

```
Objective-C
-----------

NSNotificationCenter *center = [NSNotificationCenter defaultCenter];
NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
_localeChangeObserver = [center addObserverForName:NSCurrentLocaleDidChangeNotification object:nil
    queue:mainQueue usingBlock:^(NSNotification *note) {
 
        NSLog(@"The user's locale changed to: %@", [[NSLocale currentLocale] localeIdentifier]);
    }];

Swift
-----

let center = NSNotificationCenter.defaultCenter()
let mainQueue = NSOperationQueue.mainQueue()
self.localeChangeObserver = center.addObserverForName(NSCurrentLocaleDidChangeNotification, 
	object: nil, queue: mainQueue) { (note) in
    
    print("The user's locale changed to: \(NSLocale.currentLocale().localeIdentifier)")
}
```

#### Removing observers

It is important for objects to remove observers before they are deallocated, in order to prevent further messages from being sent.

There are two methods for removing observers:`removeObserver:` and `removeObserver:name:object:`. Again, just as with adding observers, *name* and *object* are used to define scope. *removeObserver:*, or *removeObserver:name:object:* with `nil`for both parameters, will remove the observer form the notification center dispatch table entirely, while specifying parameters for *removeObserver:name:object:* will only remove the observer for registrations with that name and/or object.

#### Posting notifications

In addition to subscribing to system-provided notifications, applications may want to publish and subscribe to their own.

Notificatiosn are created with `+notificationWithName:object:userInfo:`.

Notification names are generally definedas strings constants. Like any string constant, it should be declared `extern` in public interface, and defined privately in the corresponding implementation. It does not matter to much what a notification name's value is defined to be, the name of the variable itself is commonplace, but a reverse-DNS identifier is also a classy choice. So long as notification names are unique (or explicity aliased), everything will work as expected.

Notifications are posted with `postNotificationName:object:userInfo:` or its convenience method `postNotificationName:object:, which passes *nil* for *userInfo*.

Since notification dispatch happens on the posting thread, it may be necessary to `dispatch_async`to `dispatch_get_main_queue()` so that a notification is handled on the main thread. This is not usually necessary, but it is important to keep in mind.

### KVO != NSNotificationCenter

**Key-Value Observing** adds observers for keypaths, while **NSNotificationCenter** adds observers for notifications.


## Factory (Class clusters)

## Prototype (copy, copyWithZone)

## Command (NSInvocation)

## Extensions (Objective-C categories, Swift extensions)

## Dependency injection

