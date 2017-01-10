Version Control
===============

Version Your App
----------------
Versioning is a critical component of your app upgrade and maintenance strategy. Versioning is important because:

   - It gives users information about the app version that is installed on ther devices and the versions available.
   - Determines compatibility and identifies dependencies between different apps.
   - Services may need to check the app version to determine compatibility and establish upgrade/downgrade relationships.  

Android does not use app version information to enforce restrictions on upgrades, downgrades, or compatibility of third-party apps. Instead, you are responsible for enforcing version restrictions within your app or by informing users of the version restrictions and limitations. The Android system does, however, enforce system version compatibility as expressed by the minSdkVersion setting in the build files. This setting allows an app to specify the minimum system API with which it is compatible.

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

Git Flow
--------
The Branching Model of Git Flow is particularly useful for Android development for the following reasons:

   - When needing to test user upgrade processes (database upgrades etc), checking out old versions of the code and building them is really straightforward.  
   - It is very simple to debug issues happening on production that might not be happening on development, checking out the version of code that has the crashes and debugging from there is really simple.  
   - Pull requests help improve the quality of the code, things are picked up before they are merged into development. Bugs are also found quicker.  
   - Easy to understand method even for beginners.  
   - Everyone understands how to use the branches and where branches can be made from and incorporated into. There is no guess work with regards to where a branch came from and how to merge it in.  
   - Because we do not release every single bug fix or feature into production, we decided using git flow is the best choice.


**NOTE:** For more information about Git Flow [DevOps Repository](https://github.com/beeva/beeva-best-practices/tree/master/devops/git#git-flow)

#### Changing working branch from Android Studio
Developers can change the branch they are working on  by selecting the branch they want in the bottom left corner of Android Studio. This improves the management of Git Flow from Android Studio.  

![Change branch](statics/android-studio-change-branch.png)


## Bibliography

+  Git Flow. (https://riggaroo.co.za/using-git-flow-for-android-development/)
