---
layout: page
title: "Download & Setup"
category: doc
date: 2015-05-15 21:08:05
order: 1
disqus: 1
---
* TOC
{:toc}

### Android SDK Requirements
**Minimum SDK Version** - Glide requires a minimum SDK version of **14** (Ice Cream Sandwich) or higher.

**Compile SDK Version** - Glide must be compiled against SDK version **26** (Oreo) or higher.

### Download

Glide's public releases are accessible in a number of ways.

#### Jar

You can download [the latest jar][1] from GitHub directly. Note that you will also need to include a jar for Android's [v4 support library][2].

#### Gradle

If you use Gradle you can add a dependency on Glide using either Maven Central or JCenter. You will also need to include a dependency on the support library.

```groovy
repositories {
  mavenCentral()
  maven { url 'https://maven.google.com' }
}

dependencies {
    compile 'com.github.bumptech.glide:glide:4.4.0'
    annotationProcessor 'com.github.bumptech.glide:compiler:4.4.0'
}
```

#### Maven

If you use Maven you can add a dependency on Glide as well. Again, you will also need to include a dependency on the support library.

```xml
<dependency>
  <groupId>com.github.bumptech.glide</groupId>
  <artifactId>glide</artifactId>
  <version>4.4.0</version>
  <type>aar</type>
</dependency>
<dependency>
  <groupId>com.google.android</groupId>
  <artifactId>support-v4</artifactId>
  <version>r7</version>
</dependency>
<dependency>
  <groupId>com.github.bumptech.glide</groupId>
  <artifactId>compiler</artifactId>
  <version>4.4.0</version>
  <optional>true</optional>
</dependency>
```

### Setup

Depending on your build configuration you may also need to do some additional setup.

#### Permissions
Glide does not require any permissions out of the box assuming all of the data you're accessing is stored in your application. That said, most applications either load images on the device (in DCIM, Pictures or elsewhere on the SD card) or load images from the internet. As a result, you'll want to include one or more of the permissions listed below, depending on your use cases.

##### Internet
However if you're planning on loading images from urls or over a network connection, you should add the ``INTERNET`` and ``ACCESS_NETWORK_STATE`` permissions to your ``AndroidManifest.xml``:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.INTERNET"/>
    <!--
    Allows Glide to monitor connectivity status and restart failed requests if users go from a
    a disconnected to a connected network state.
    -->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application>
      ...
    </application>
</manifest>
```

``ACCESS_NETWORK_STATE`` isn't technically required to allow Glide to load urls, but it helps Glide handle flaky network connections and airplane mode. See the Connectivity Monitoring section below for more details

##### Connectivity Monitoring
If you're loading images from urls, Glide can automatically help you deal with flaky network connections by monitoring users' connectivity status and restarting failed requests when users are reconnected. If Glide detects that your application has the ``ACCESS_NETWORK_STATE``, Glide will automatically monitor connectivity status and no further changes are needed. 

You can verify that Glide is monitoring network status by checking the ``ConnectivityMonitor`` log tag:

```
adb shell setprop log.tag.ConnectivityMonitor DEBUG
```

After doing so, if you've successfully added the ``ACCESS_NETWORK_STATE`` permission, you will see logs in logcat like:

```
11-18 18:51:23.673 D/ConnectivityMonitor(16236): ACCESS_NETWORK_STATE permission granted, registering connectivity monitor
11-18 18:48:55.135 V/ConnectivityMonitor(15773): connectivity changed: false
11-18 18:49:00.701 V/ConnectivityMonitor(15773): connectivity changed: true
```

If the permission is missing, you'll see an error instead:

```
11-18 18:51:23.673 D/ConnectivityMonitor(16236): ACCESS_NETWORK_STATE permission missing, cannot register connectivity monitor
```

##### Local Storage
To load images from local folders like DCIM or Pictures, you'll need to add the ``READ_EXTERNAL_STORAGE`` permission:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />

    <application>
      ...
    </application>
</manifest>
```

To use [``ExternalPreferredCacheDiskCacheFactory``][7] to store Glide's cache on the public sdcard, you'll need to use the 
``WRITE_EXTERNAL_STORAGE`` permission instead:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="your.package.name"

    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

    <application>
      ...
    </application>
</manifest>
```

#### Proguard

If you use proguard, you may need to add the following lines to your ``proguard.cfg``:

```
-keep public class * implements com.bumptech.glide.module.GlideModule
-keep public class * extends com.bumptech.glide.module.AppGlideModule
-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {
    **[] $VALUES;
    public *;
}
```

#### Jack
Glide's build configuration requires features that [Jack][3] does not currently support. Jack was recently [deprecated][4] and it's unlikely that the features Glide requires will ever be added. If you'd like to compile with Java 8, see below.

#### Java 8
Starting with Android Studio 3.0 and version 3.0 of the Android Gradle plugin, you can compile your project and Glide with Java 8. For details, see the [Use Java 8 Language Features][5] on the Android Developers website. 

Glide itself does not use or require you to use Java 8 to compile or use Glide in your project. Glide will eventually require Java 8 to compile, but we will do our best to allow time for developers to update their applications first, so it's likely that Java 8 won't be a requirement for months or years (as of 11/2017).

#### Kotlin

If you use Glide's annotations on classes implemented in Kotlin, you need to include a ``kapt`` dependency on Glide's annotation processor instead of a ``annotationProcessor`` dependency:

```groovy
dependencies {
  kapt 'com.github.bumptech.glide:compiler:4.4.0'
}
```
Note that you must also include the ``kotlin-kapt`` plugin in your ``build.gradle`` file:

```groovy
apply plugin: 'kotlin-kapt'
```

See the [generated API][6] page for details.

[1]: https://github.com/bumptech/glide/releases/download/v3.6.0/glide-3.6.0.jar
[2]: http://developer.android.com/tools/support-library/features.html#v4
[3]: https://source.android.com/source/jack
[4]: https://android-developers.googleblog.com/2017/03/future-of-java-8-language-feature.html
[5]: https://developer.android.com/studio/write/java8-support.html
[6]: {{ site.baseurl }}/doc/generatedapi.html#kotlin
[7]: {{ site.baseurl }}/javadocs/431/com/bumptech/glide/load/engine/cache/ExternalPreferredCacheDiskCacheFactory.html
