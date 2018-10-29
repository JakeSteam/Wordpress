---
ID: 1728
post_title: Adding Firebase to an Android Project
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/adding-firebase-to-an-android-project/
published: true
post_date: 2018-09-27 21:49:17
---
Considering the vast array of features included in Firebase, adding it to your project is surprisingly easy. Later versions of Android Studio even include an assistant that analyses the current project and provides fixes for common integration mistakes.

This guide however, will focus on the very straightforward steps required to integrate Firebase manually.

<!--more-->
<h2>Prerequisites</h2>
<ol>
 	<li>An existing <a href="https://blog.jakelee.co.uk//2018/09/23/creating-a-new-firebase-project/">Firebase project</a></li>
 	<li>An existing Android Studio project (<a href="https://github.com/JakeSteam/FirebaseReference/releases/tag/no-integration" target="_blank" rel="noopener">an example project is available</a>)</li>
</ol>
<h2>Google Services file</h2>
The next step is obtaining a <code>google-services.json</code> file, so that the app knows which Firebase project to talk to. This can be done by visiting <a href="https://console.firebase.google.com/project/_/overview" target="_blank" rel="noopener">the project overview</a> and clicking the Android icon.
<img class="alignnone size-full wp-image-1729" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/ylq8bsv.png" alt="ylq8bsv" width="953" height="329" />

This will bring up the "Add Firebase" wizard:
<img class="alignnone size-full wp-image-1730" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/gg9tpzq.png" alt="gg9tpzq" width="664" height="606" />
<ol>
 	<li><b>Android package name</b>: The only required part of this form, this is the package name used when creating your app. It will be visible in the <code>AndroidManifest.xml</code> file as <code>package="com.example.test"</code>.</li>
 	<li><b>App nickname</b>: This is an optional "friendly" name you can use to better summarise the app.</li>
 	<li><b>Debug signing certificate</b>: This is not needed for most aspects of Firebase, but <a href="https://developers.google.com/android/guides/client-auth" target="_blank" rel="noopener">a guide does exist</a>.</li>
</ol>
Once 1-3 of the fields have been filled in, the app can now be registered (4). Clicking this button will provide a download link for your <code>google-services.json</code> file, as well as instructions on where to put it. Switch to "Project" view in the left Android Studio sidebar, then put the downloaded file inside the "app" folder.
<h2>Firebase SDK</h2>
Next, the Firebase library needs to be added to your project. Doing this is just 4 simple steps:
<ol>
 	<li>Add <code>classpath 'com.google.gms:google-services:4.0.1'</code> inside the <code>dependencies</code> section of your project level <code>build.gradle</code> (file will have "(Project: yourappname)" next to it).</li>
 	<li>Add <code>implementation 'com.google.firebase:firebase-core:16.0.3'</code> inside the <code>dependencies</code> section of your app level <code>build.gradle</code> (file will have "(Module: app)" next to it).</li>
 	<li>Also add <code>apply plugin: 'com.google.gms.google-services'</code> to the bottom of the app level <code>build.gradle</code>.</li>
 	<li>Finally, press "Sync now" in the yellow bar at the top.</li>
</ol>
<i>Note: As of writing, 16.0.3 is the latest Firebase version, and 4.0.1 the latest Google services version. If these are outdated, Android Studio will prompt you to update.</i>
<h2>Final steps</h2>
Finally, just run your app, and the "Add Firebase" wizard will confirm Firebase has been set up correctly:
<img class="alignnone size-full wp-image-1732" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/dkl3twd.png" alt="dkl3twd" width="744" height="219" />

That's it, Firebase is fully set up! Your project settings (e.g. name) <a href="https://console.firebase.google.com/project/_/settings/general/" target="_blank" rel="noopener">can be changed later</a> if necessary.

Previous: <a href="https://blog.jakelee.co.uk//creating-a-new-firebase-project/">Creating A New Firebase Project</a>
Next: <a href="https://blog.jakelee.co.uk//developing-android-apps-with-firebase-authentication/">Developing Android Apps With Firebase Authentication</a>