---
ID: 1786
post_title: >
  Fixing Dependency Conflict In OneSignal
  Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/fixing-dependency-conflict-in-onesignal-android/
published: true
post_date: 2018-10-23 15:00:04
---
Whilst following OneSignal's <a href="https://documentation.onesignal.com/docs/android-sdk-setup" target="_blank" rel="noopener">setup guide</a> for their <a href="https://github.com/OneSignal/OneSignal-Android-SDK" target="_blank" rel="noopener">Android SDK</a>, everything went smoothly until attempting to run the build past our CI (continuous integration) server, <a href="https://circleci.com/" target="_blank" rel="noopener">CircleCI</a>. I've <a href="https://blog.jakelee.co.uk//getting-onesignal-working-on-a-multi-module-project/">encountered various issues</a> when using custom build flavours with OneSignal before, and expect that was the cause of this issue too.

The build failed, due to the following exception:
<pre>Execution failed for task ':app:preCustomflavourQaAndroidTestBuild'.
&gt; Conflict with dependency 'com.google.firebase:firebase-messaging' in project ':app'. Resolved versions for app (17.0.0) and test app (12.0.1) differ. See https://d.android.com/r/tools/test-apk-dependency-conflicts.html for details.</pre>
<!--more-->

As Firebase messaging was not used in any modules inside the project, only by OneSignal, the conflict was a little confusing. Was it... conflicting with itself!? Regardless, the solution was relatively straightforward, simply forcing Firebase Messaging to <b>always</b> resolve to the correct version (17.0.0) fixed the test build.

Inside the app-level <code>build.gradle</code>, add:
<pre>configurations.all {
    resolutionStrategy {
        force "com.google.firebase:firebase-messaging:17.0.0"
    }
}</pre>
This change fixed the test build, and our CI was back to a lovely sea of green success messages!