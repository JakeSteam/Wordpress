---
ID: 1817
post_title: >
  TokenRefresher Fatal Crash When Using
  FirebaseUI
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/tokenrefresher-fatal-crash-when-using-firebaseui/
published: true
post_date: 2018-10-28 15:02:24
---
<a href="https://github.com/firebase/FirebaseUI-Android" target="_blank" rel="noopener">FirebaseUI</a> is a very useful collection of UI components for Firebase. It removes some of the complexity of implementing Firebase components by providing ready made UI components that your code can hook into. The <a href="https://github.com/firebase/FirebaseUI-Android/blob/master/auth/README.md" target="_blank" rel="noopener">FirebaseUI-auth library</a> is used for Firebase Authentication in the <a href="https://blog.jakelee.co.uk//firebase/">Firebase series</a>' reference app, as well as various other Firebase dependencies.

However, when first running the app, occasionally it would crash with a somewhat cryptic error:
<pre>	10-24 21:42:00.247 17892-17941/uk.co.jakelee.firebasereference E/AndroidRuntime: FATAL EXCEPTION: TokenRefresher
    Process: uk.co.jakelee.firebasereference, PID: 17892
    java.lang.NoSuchFieldError: No field PREFER_HIGHEST_OR_REMOTE_VERSION_NO_FORCE_STAGING of type Lcom/google/android/gms/dynamite/DynamiteModule$VersionPolicy; in class Lcom/google/android/gms/dynamite/DynamiteModule; or its superclasses (declaration of 'com.google.android.gms.dynamite.DynamiteModule' appears in /data/app/uk.co.jakelee.firebasereference-2/base.apk:classes3.dex)
        at com.google.android.gms.flags.FlagValueProvider.initialize(Unknown Source)
        at com.google.android.gms.flags.FlagRegistry.initialize(Unknown Source)
        at com.google.firebase.auth.internal.zzx.initialize(Unknown Source)
        at com.google.firebase.auth.internal.zzt.run(Unknown Source)
</pre>
<!--more-->

After looking through my commit history, I realised the problem started appearing when I previously attempted to fix a mismatched libraries error:
<pre>The library com.google.android.gms:play-services-basement is being requested by various other libraries at [[15.0.1,15.0.1]], but resolves to 16.0.1. Disable the plugin and check your dependencies tree using ./gradlew :app:dependencies.
</pre>
To resolve this, I'd added a temporary workaround in my <code>build.gradle</code>, which I completely forget to fix properly:
<pre>GoogleServicesPlugin.config.disableVersionCheck = true</pre>
Removing this version check disabler, and actually dealing with the root cause (mismatched libraries) led to the realisation that the FirebaseUI auth dependency was using an older version of <code>play-services-basement</code>.

Updating the <code>com.firebaseui:firebase-ui-auth</code> dependency to 4.2.1 from 4.1.0 fixed the problem instantly. The "quick fix" was no quicker or easier than actually fixing it properly!