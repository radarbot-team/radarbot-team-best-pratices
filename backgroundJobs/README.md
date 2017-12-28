# Best Practices for Background Jobs

An Android application has at least one main thread and in Android, you shouldn’t do anything that blocks the main thread. For work with several thread the Android framework offers different options like [Threads](https://developer.android.com/reference/java/lang/Thread.html), [HandlerThread](https://developer.android.com/reference/android/os/HandlerThread.html), [Executor](https://developer.android.com/reference/java/util/concurrent/Executor.html), [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask.html) or [Services](https://developer.android.com/reference/android/app/Service.html).

### Thread
The [threads](https://developer.android.com/reference/java/lang/Thread.html) are the solution for work with background operations that don't need to be notified to the main thread. When we using threads, need avoid the synchronization inside loops because acquiring and releasing a lock is an expensive operation. 

### HandlerThread

Android provides a thread subclass, [HandlerThread] (https://developer.android.com/reference/android/os/HandlerThread.html), which encapsulates the Looper object (so that we do not care the Looper open and release details) and the MessageQueue. 

Once we started HandlerThread we can post tasks to it at any time, the HandlerThread remains active until you call `quit`, remember make this call for free resources when you don't need the HandlerThread anymore.

### Executor
The [Executor](https://developer.android.com/reference/java/util/concurrent/Executor.html) class is powerful and allow automatically manage a pool of threads with a task queue. The Executor allows you to get all the scheduled tasks and cancel them if you'd like.


### AsyncTask
[AsyncTask](https://developer.android.com/reference/android/os/AsyncTask.html) allows you to perform background operations and return results on the main thread without having to manipulate threads or handlers. 

The most important thing you should know is that only one method of this class that is running on another thread `doInBackground`. The other methods are running on main thread.

The AsyncTask is used inside an Activity, you need to make sure that it doesn't have a reference to activity or views when activity is already destroyed. This means that they are not the best option for long running operations and also, if the app is in the background and the app is terminated by Android, your background processing is also terminated.

Implementing AsyncTask as an inner class makes sense only if it's static. If it's not static then it will have an implicit reference to outer activity which will lead to memory leaks. If you need references to views from your static async task use [WeakReference](https://developer.android.com/reference/java/lang/ref/WeakReference.html).
Implementing AsyncTask in a separate file is also a good idea, but same rules applied. Use weak references if needed.
The only difference between static inner async task and async task in a separate file is code readability. If there is a lot of logic inside AsyncTask, go ahead with a separate file.


### Services
The Android framework offers several classes that help you with the operations can cause problems and interfere with the responsiveness of your user interface, the most useful of these is [Service](https://developer.android.com/reference/android/app/Service.html). 

For doing long-term background tasks you should use services. The service has no user interface, it is not bound to the lifecycle of an activity. A service can essentially take two shapes: 

* **Started**: A service is started when an application component starts it by calling `startService()`. Once started, a service can run in the background indefinitely, even if the component that started it is destroyed, and it is active until it has called the `stopService()` method. 

	A Started Service is helpful to handle multiple simultaneous request. 
	
* **Bound**: A service is bound when an application component binds to it by calling `bindService()`. A bound service offers a client-server interface that allows components to interact with the service, send requests, get results, and even do so across processes with interprocess communication.

	
	A Bound  Service is helpful if you need a direct communication between a component and the service. This services holds a reference to the clients and, when no more clients are referenced, the service is automatically terminated. This behavior is helpful when we need to share a background operation between multiple activities without the need to close the service because it is terminated automatically


#### IntentService
You can also extend the `IntentService` class for your service implementation. It is a particular implementation of a service provided by the patform. The [IntentService](https://developer.android.com/reference/android/app/IntentService.html) is used to perform a certain task in the background, a good example would be to upload or download large files, the upload and download may continue even if the user exits the app and you don't want to block the user from being able to use the app while these tasks are going on. Once done, the instance of IntentService terminates itself automatically. 

If you want to run a task repeatedly on different sets of data, but you only need one execution running at a time, an IntentService suits your needs.