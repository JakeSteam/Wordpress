---
ID: 2546
post_title: >
  How to create (and test) an app update
  listener
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-create-and-test-an-app-update-listener/
published: true
post_date: 2019-06-10 12:55:33
---
Running code when your app updates can be a useful marketing tool, and a reliable way of enabling new functionality only when the user updates.

For example, your app may send a message to your server when it updates, so your server knows it can now return more types of content. Similarly, you may want to send a notification to the user (from the app) when it updates, informing them of new content.

Whilst there's plenty of existing documentation on registering an update listener, much of it is outdated, unsafe, or doesn't include any information on how to test your implementation. This article will hopefully address these shortcomings, and is also available <a href="https://github.com/JakeSteam/UpdateListener" target="_blank" rel="noopener noreferrer">as a repository</a> and <a href="https://gist.github.com/JakeSteam/4561f0f46a7b08be50785a72559f8fef" target="_blank" rel="noopener noreferrer">a Gist</a>.<!--more-->

The approach is very simple; we're just registering an app update listener in the manifest, and then displaying a toast message inside this listener.
<h2>Registering receiver</h2>
The <code>MY_PACKAGE_REPLACED</code> intent action is only fired when your app is updated. It is only available on APIs 12+ (which should be fine for almost all apps), and replaces the existing <code>PACKAGE_REPLACED</code>. The drawback of the previous technique is it would often be called when <strong>any</strong> app updates, causing your app to be woken up extremely frequently.

This receiver (<code>.AppUpgradeReceiver</code> in this case) is registered in your <code>AndroidManifest.xml</code>, within the <code>application</code> tag:
<pre>&lt;receiver android:name=".AppUpgradeReceiver"&gt;
    &lt;intent-filter&gt;
        &lt;action android:name="android.intent.action.MY_PACKAGE_REPLACED" /&gt;
    &lt;/intent-filter&gt;
&lt;/receiver&gt;</pre>
<h2>Handling update intent</h2>
Now we need to create the <code>AppUpgradeReceiver</code> receiver we just defined. This class extends <code>BroadcastReceiver</code>, which has an <code>onReceive</code> that needs overriding:
<pre>class AppUpgradeReceiver : BroadcastReceiver() {

    @SuppressLint("UnsafeProtectedBroadcastReceiver")
    override fun onReceive(context: Context?, intent: Intent?) {
        if (context == null) {
            return
        }
        Toast.makeText(context, "Updated to version #${BuildConfig.VERSION_CODE}!", Toast.LENGTH_LONG).show()
    }

}</pre>
Note that lint complains that we're not filtering the incoming intent (to make sure it's for our package), but this is not needed as we are using the <code>MY_PACKAGE_REPLACED</code> intent. The <code>SuppressLint</code> annotation should only be used when you are intentionally disagreeing with lint's analysis, as in this situation!

And that's it! Your listener is registered, and will show a Toast message of the new version code whenever your app updates.
<h2>Testing</h2>
Testing this functionality initially seems intimidating, as uploading a new version to the Play Store every time would be very time consuming; luckily, we can update apps yourselves!

Previously, testing just required running the new version of your app from Android Studio. Now however, the <code>MY_PACKAGE_REPLACED</code> intent can only be sent by the OS, so is not triggered during normal development.

The solution is to actually install the app manually:
<ol>
 	<li>Increasing the <code>versionCode</code> in your app-level <code>build.gradle</code> (so it counts as an update).</li>
 	<li>Clicking <code>Build</code> -&gt; <code>Build Bundle(s) / APK(s)</code> -&gt; <code>Build APK(s)</code>, and selecting a debug APK.</li>
 	<li>Entering the following command into the <code>Terminal</code> tab at the bottom of Android Studio:</li>
</ol>
<pre>adb install -r &lt;path to your apk&gt;</pre>
For example, the command on a Windows machine to update <a href="https://github.com/JakeSteam/UpdateListener" target="_blank" rel="noopener noreferrer">this tutorial's app</a> may look like:
<pre>adb install -r C:\Repositories\updatelistener\app\build\outputs\apk\debug\app-debug.apk</pre>
You should see "Success" underneath your command in the terminal.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Annotation-2019-06-10-125106.jpg"><img class="aligncenter size-full wp-image-2548" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Annotation-2019-06-10-125106.jpg" alt="" width="842" height="154" /></a>

The app is then updated like normal, and your listener called. In <a href="https://github.com/JakeSteam/UpdateListener" target="_blank" rel="noopener noreferrer">the sample repo</a>, an update will cause the new version code to be displayed in a Toast.