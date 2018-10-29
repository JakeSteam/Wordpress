---
ID: 1795
post_title: >
  Filtering Google / Firebase Analytics
  Traffic By BuildType / Environment On
  Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/filtering-google-firebase-analytics-traffic-by-buildtype-environment-on-android/
published: true
post_date: 2018-10-26 17:00:56
---
Firebase Analytics / Google Analytics is the most widely used service for analysing traffic, and can be used to track detailed information about how users use your app. However, by default it will track <b>all</b> information, including any logged during development or QA. This may mean that the new page you're working on seems to have a massive spike in traffic, potentially misleading any marketing efforts.

Whilst this tutorial covers Firebase Analytics especially, the steps are identical for Google Analytics. Note that only apps added as a "Mobile app" (e.g. using Firebase) can use user properties.

<!--more-->
<h2>Creating the user property</h2>
First, go to the "User Properties" link in the sidebar of the Firebase dashboard, and click the "New User Property" button.
<img class="alignnone size-full wp-image-1796" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/alnvs2o.png" alt="alnvs2o" width="994" height="278" />

Enter "Environment" as your user property name, and an optional description, then press "Create", and your new user property will be in the list. Please note that User Properties <b>cannot be deleted or renamed</b>, so type them carefully!
<img class="alignnone size-full wp-image-1797" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/zzqo4b4.png" alt="zzqo4b4" width="1170" height="156" />
<h2>Setting the user property</h2>
Now the user property has been created, values can be set for it. This tutorial assumes you've already <a href="https://firebase.google.com/docs/analytics/android/start/" target="_blank" rel="noopener">added Firebase Analytics for Android</a>.

The easiest way to do this is to just send the current <code>BuildConfig.BUILD_TYPE</code>, as this ensures any future custom build types (e.g. "QA" or "Preprod") will be sent automatically. However, custom logic can of course be used instead.

This should be placed inside the <code>onCreate</code> or <code>onStart</code> of your application, with the rest of your initialisations.
<pre>
FirebaseAnalytics.getInstance(this).setUserProperty("Environment", BuildConfig.BUILD_TYPE)</pre>
<h2>Filtering by user property</h2>
Once the app has been run with a user property set, the new value will be automatically added to Analytics to be filtered on. To apply the filter, click the grey dotted "Add Filter" button in the top left of Analytics, select "User Property", and select the Environment values you'd like to view data for. In the example below, only data from preprod and release environments will be shown, omitting all development or testing data.
<img class="alignnone size-full wp-image-1798" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/ty3mwzp.png" alt="ty3mwzp" width="690" height="330" />

Now that Analytics data is filtered to only include your real customers, the more accurate representation should help give a better idea of a user's experience inside your app, and how to improve it.