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



