---
ID: 1719
post_title: Creating A New Firebase Project
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-new-firebase-project/
published: true
post_date: 2018-09-23 20:18:10
---
The first step towards integrating any of the 25+ features included in Firebase is creating a new Firebase project. The same project can be used across for multiple apps, but only one per platform. For example, a web app, Android app, and iOS app can all use the same project. This post is part of the <a href="https://blog.jakelee.co.uk//firebase/">Firebase tutorial series</a>.

<!--more-->
<h3>Firebase console</h3>
Visiting the <a href="https://console.firebase.google.com/u/0/" target="_blank" rel="noopener">Firebase console page</a> displays an overview of all existing projects, a link to a demo project (requires log-in), as well as a large button to create a new project.
<h3>Create project form</h3>
Clicking the new project button displays the following screen:
<img class="alignnone size-full wp-image-1722" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/create-firebase-project.png" alt="create-firebase-project" width="586" height="709" />
<ol>
 	<li><b>Project name</b>: This can be anything you want, and will never be seen by the user. There's no need to include the platform in this name, as the same project can be used across Android, iOS, and web.</li>
 	<li><b>Project ID</b>: This is a globally unique identifier for this project, so many common identifiers will already be taken. Firebase will automatically suggest a reasonable name for you, but you can override this if desired. Again, it will never be seen by the user. This cannot be changed later.</li>
 	<li><b>Analytics location</b>: This defaults to US, but should be changed to your actual country as it affects the currency revenues are displayed in.</li>
 	<li><b>Cloud Firestore location</b>: This (unsurprisingly) sets where Cloud Firestore stores data. Note that there are only 3 regions available as of this article's publication, 2 of which are in the US. The only non-US location (west europe) doesn't support some features (e.g. Cloud Functions).</li>
 	<li><b>Default settings</b>: This setting toggles sharing Analytics data with Google. This is recommended, as it improves the service in general, and also allows Google's technical support to diagnose any potential issues.</li>
 	<li><b>Controller-controller terms</b>: This setting is only available when <b>Default settings</b> are enabled, and confirms that data should be shared according to the <a href="https://support.google.com/analytics/answer/9012600" target="_blank" rel="noopener">Data Protection Terms</a>.</li>
</ol>
<h3>Submitting form</h3>
Once "Create project" is clicked, it may take up to 30 seconds to actually create your project, during which you will see the screen on the left, followed eventually by the screen on the right:
<img class="alignnone size-full wp-image-1723" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/creating-firebase-project.png" alt="creating-firebase-project" width="1143" height="531" />

When "Continue" is clicked, project setup is complete! You'll then be taken to the Firebase dashboard, and prompted to integrate Firebase into your app. This topic will be covered in the next post. For help with implementing a specific Firebase feature, please view the <a href="https://blog.jakelee.co.uk//firebase/">Firebase series page</a>.

Next: <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Adding Firebase to an Android Project</a>