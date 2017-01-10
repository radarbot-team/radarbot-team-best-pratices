Project Configuration
=====================

Configure build variants in gradle
----------------------------------

**Establish applicationId in build.gradle**  
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
**Configuration for the release version**  
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
**Configuration of the keystore file**  
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

**Dependencies**  
In general, it is important to obtain the dependencies of our project from a repository and not from a local .jar file since it will be easier to update our dependency to its ultimate version, specially in projects where there is more than one developer, which is the normal case.
It is recommended to have a separate file with the version of each dependency so that if a different module needs to use one of these dependencies, the version used will be the same as the one used in the main module for the same dependency.

```groovy
// fichero dependencies.gradle
ext {
  supportLibraryVersion = ‘25.1.0’
  butterknifeVersion = ‘8.4.0’
  // ...
}

// fichero build.gradle
dependencies {
  // ...
  compile "com.android.support:cardview-v7:$supportLibraryVersion"
  // ...
}
```
An exact version must be indicated, i.e. "25.1.0", and not a range, i.e. "25.+", so that we do not find an unexpected change in our dependency.  

*<ins>Google Play Services and Firebase</ins>*  
We must not import all the dependencies from Google Play Services and Firebase. These libraries contain all the native packages for Android, so, if we import them, we could pass the 65K methods limit and we would be importing code that we won’t probably need or use. To avoid this, we must import only the libraries that we are really going to use. You can check the libraries in Google Play Services [here](https://developers.google.com/android/guides/setup#add_google_play_services_to_your_project) and the ones in Firebase [here](https://firebase.google.com/docs/android/setup?hl=en#available_libraries).

Structure of folders and packages
---------------------------------
####Code
The structure of packages we use will be related to the architecture we implement.

####Resources

***Images***  
The images must be generated for each density and they should be stored in their corresponding “drawable-density” folder. The root folder `drawable` should only be used to store xml files such as selectors, layer-lists, ....
The icons of the app must be stored in the folders named `mipmap` of each density.

***Layouts***  
Layouts must be named after the type of file or component they are implementing. For example, in case a layout is implementing the view of an activity, this layout should be named activity_….xml.


***Strings***  
It is not recommended to have a unique strings.xml file with all the strings used by our application. It is best to create different strings_….xml files grouped by functionality.

***Colors and Dimensions***  
The names for colors and dimensions should be as generic as possible so that they can be used in different places of our application and be changed without unexpected results. For example:  

instead of having

```xml
<color name="button_background">#FFFFFF</color>
```

we should have

```xml
<color name="white">#FFFFFF</color>
```
