---
ID: 1596
post_title: >
  Getting OneSignal Working On A
  Multi-Module Project
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/getting-onesignal-working-on-a-multi-module-project/
published: true
post_date: 2018-08-19 19:12:41
---
Recently, upon attempting to implement OneSignal for user notifications (and following <a href="https://documentation.onesignal.com/docs/android-sdk-setup" target="_blank" rel="noopener">their installation instructions</a>), a wide variety of intriguing and mysterious build errors were encountered.

The root cause of these seemed to be their gradle plugin (ironically intended to simplify the dependency process, and solve any Google Play Services issues) causing issues when attempting to be applied to a project with 10+ modules inside it. Luckily, the fix was pretty straight forward.

<!--more-->

First, a few of the errors encountered:

<ul>
    <li><code>com.google.android.gms:play-services-measurement-base is being requested by various other libraries</code></li>
    <li><code>Android dependency is set to compileOnly/provided which is not supported</code></li>
    <li><code>Error: Program type already present: com.google.android.gms.internal.measurement.zzwo</code></li>
    <li><code>java.lang.NoClassDefFoundError: com.google.android.gms.gcm.GoogleCloudMessaging</code></li>
    <li><code>Duplicate key com.android.build.gradle.internal.api.artifact.BuildableArtifactImpl@7c139f68</code></li>
</ul>

The reason behind these issues ultimately boiled down to:

<ol>
    <li>The Gradle plugin being useful in an ideal situation, but just muddying the waters if trying to resolve an issue manually.</li>
    <li>The <a href="https://github.com/OneSignal/OneSignal-Android-SDK/blob/master/OneSignalSDK/onesignal/build.gradle" target="_blank" rel="noopener">library's `build.gradle`</a> containing a few old Google Play Services dependencies (e.g. 12.0.1), and some unusual syntax.</li>
<li>The OneSignal library not being designed for a project with many modules.
</ol>

The solution settled on was unfortunately not at all ideal, but it <i>did</i> get notifications working perfectly:

<ol>
<li>Remove all code to do with the Gradle plugin, it's not necessary.
<li>Download the module as a `.zip` and import it into your project manually.
<li>Update the Google Play Services version to the same as the rest of your project.
<li>Add `implementation 'com.google.android.gms:play-services-ads:15.0.1'` to the new submodule's `build.gradle`, or a few files within won't compile.
<li>Fix a couple of files that have missing imports.
<li>Resync Gradle, and skip to <a href="https://documentation.onesignal.com/docs/android-sdk-setup" target="_blank">Step 2 of the OneSignal setup docs</a>. 
<li><i>Note: If you're using build flavours, make sure you change those in the new submodule to match your own.</i>
</ol>

Whilst the solution did work, unfortunately it's a very hacky approach, just downloading the library and manually messing the the gradle files. Hopefully in the future the OneSignal library's multi module support improves.