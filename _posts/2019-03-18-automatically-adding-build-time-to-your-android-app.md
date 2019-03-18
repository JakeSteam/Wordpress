---
ID: 2431
post_title: >
  Automatically adding build time to your
  Android app
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/automatically-adding-build-time-to-your-android-app/
published: true
post_date: 2019-03-18 22:12:06
---
When releasing an Android app, it can be useful to show your users the current version name (<code>BuildConfig.VERSION_NAME</code>) or code (<code>BuildConfig.VERSION_CODE</code>). Another nice feature is showing when this version of the app was built, to reassure users that features / bug fixes are being released frequently.

Luckily, this can be done in 2 super simple steps:<!--more-->

First, add <code>BUILD_TIME</code> (of type <code>Date</code>) into your app-level <code>build.gradle</code>, with a value defined using the <code>Date</code> constructor with the current time. So, inside <code>defaultConfig</code>:
<pre>buildConfigField "java.util.Date", "BUILD_TIME", "new java.util.Date(" + System.currentTimeMillis() + "L)"</pre>
Next, just use <code>BuildConfig.BUILD_TIME</code> whenever you want to access this date object. Just like a normal <code>Date</code> object, it can be formatted (e.g. to "18 Mar 2019"):
<pre>SimpleDateFormat("dd MMM yyy", Locale.US).format(BuildConfig.BUILD_TIME)</pre>
That's it!

This technique is used in <a href="https://github.com/JakeSteam/APODWallpaper/blob/master/app/build.gradle#L15" target="_blank" rel="noopener noreferrer">my open source APOD Wallpaper app</a>, and <a href="https://gist.github.com/JakeSteam/6052f0f3a7ac523649c4f05d1d1cb1fb" target="_blank" rel="noopener noreferrer">available as a Gist</a>.