---
ID: 2045
post_title: 'Ensuring Your Android App&#8217;s Quality With Firebase Crashlytics'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/ensuring-your-android-apps-quality-with-firebase-crashlytics/
published: true
post_date: 2018-11-25 17:31:48
---
Firebase Crashlytics provides an extremely powerful automatic crash reporting tool for Android (and iOS) apps. In addition to providing a full stack trace for all crashes, it also tracks all ANRs (App Not Responding) in your app. All crashes / ANRs come with lots of metadata about the device, operating system, and can be enhanced with custom data

This data is then aggregated, and widespread issues can be quickly identified and prioritised. Unlimited Crashlytics usage is provided for free, and basic integration can be completed in a couple of minutes.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you’ll need access to the <a href="https://console.firebase.google.com/u/0/project/_/crashlytics" target="_blank" rel="noopener">Firebase Crashlytics dashboard</a>, and the <a href="https://firebase.google.com/docs/crashlytics/" target="_blank" rel="noopener">official documentation</a> may be useful.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/9" target="_blank" rel="noopener">pull request for adding Firebase Crashlytics</a> if you just want to see the code changes required. This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.

In this tutorial, you'll learn how to implement Crashlytics into your existing app. You'll also learn how to customise these crash reports, receive crash reports through various platforms, and view your crashes online.
<h3>Setting up Crashlytics</h3>
<h4>Web interface</h4>
On the Crashlytics web interface, click "Set up Crashlytics", then select "This app is new to Crashlytics" and press next. That's it, Crashlytics is now listening for crashes, time to make your app send them!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/vHVuA82.png"><img class="alignleft size-full wp-image-2048" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/vHVuA82.png" alt="" width="648" height="396" /></a>
<h4>Project-level <code>build.gradle</code></h4>
In your project-level <code>build.gradle</code>, add the following inside <code>buildscript</code> -&gt; <code>repositories</code>:
<pre class="prettyprint devsite-disable-click-to-copy devsite-code-highlight"><strong><span class="pln">        maven </span><span class="pun">{</span><span class="pln">
           url </span><span class="str">'https://maven.fabric.io/public'</span><span class="pln">
        </span><span class="pun">}</span></strong></pre>
And inside <code>buildscript</code> -&gt; <code>dependencies</code>:
<pre class="prettyprint devsite-disable-click-to-copy devsite-code-highlight"><strong><span class="pln">        classpath </span><span class="str">'com.google.gms:google-services:4.2.0'</span><span class="pln">
        classpath </span><span class="str">'io.fabric.tools:gradle:1.26.1'</span></strong></pre>
Finally, inside <code>allprojects</code> -&gt; <code>repositories</code>:
<pre class="prettyprint devsite-disable-click-to-copy devsite-code-highlight"><strong><span class="pln">       maven </span><span class="pun">{</span><span class="pln">
           url </span><span class="str">'https://maven.google.com/'</span><span class="pln">
       </span><span class="pun">}</span></strong></pre>
<h4>App-level <code>build.gradle</code></h4>
At the top of the file, add:
<pre class="prettyprint devsite-disable-click-to-copy devsite-code-highlight"><strong><span class="pln">apply plugin</span><span class="pun">:</span> <span class="str">'io.fabric'</span></strong></pre>
Finally, inside <code>dependencies</code>:
<pre class="prettyprint devsite-disable-click-to-copy devsite-code-highlight"><span class="pln">    </span><strong><span class="pln">implementation </span><span class="str">'com.google.firebase:firebase-core:16.0.4'</span><span class="pln">
    implementation </span><span class="str">'com.crashlytics.sdk.android:crashlytics:2.9.6'</span></strong></pre>
Now, perform a Gradle sync. Crashlytics is enabled! You can test this by throwing any exception, or using the built-in <code>Crashlytics.getInstance().crash()</code>.
<h3>Enabling &amp; disabling crash reporting</h3>
By default, Crashlytics will run automatically. However, if you want to disable crash reporting until a certain time (e.g. the user accepts their crash data being shared), it can be enabled on demand. Note that crash reporting cannot be turned off programmatically, the user must restart the app. First, disable Firebase in your <code>AndroidManifest.xml</code>:
<pre> &lt;meta-data
      android:name="firebase_crashlytics_collection_enabled"
      android:value="false" /&gt;</pre>
Now, at whichever point in the user flow you want to enable Crashlytics, you need to initialise it:
<pre class="prettyprint lang-java"><span class="typ">Fabric</span><span class="pun">.</span><span class="pln">with</span><span class="pun">(</span><span class="kwd">this</span><span class="pun">,</span> <span class="kwd">new</span> <span class="typ">Crashlytics</span><span class="pun">());</span></pre>
All crashes / ANRs will now be recorded.
<h3>Adding custom logs &amp; data to Crashlytics</h3>
<h4>Logging</h4>
If you are already using Firebase Analytics, any screen view events in there will appear under the "Logs" tab of your crash.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/logs-1.png"><img class="aligncenter wp-image-2051 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/logs-1-300x261.png" alt="" width="300" height="261" /></a>

You can also add your own events to this log manually. These can be added with <code>Crashlytics.log("Your log message")</code>, and will then show up in your logs.
<h4>Keys</h4>
You can also add custom "keys" to your crashes. This is useful for recording the current state of a user when the crash occurred, for example if they are a new or returning user. This is done using <code>Crashlytics.setString</code>, <code>.setBool</code>, <code>setDouble</code>, <code>setFloat</code>, or <code>setInt</code>. These functions all take a key string (e.g. "isNewUser"), and a value (e.g. "true"). These can be overwritten at any time.
<h4>Identifiers</h4>
It is often useful to know exactly which user experienced the crash, so you can work towards reproducing and eventually fixing the issue. This is done by calling <code>Crashlytics.setUserIdentifier("user1234")</code>.
<h3>Receiving Crashlytics alerts</h3>
By default, you will receive email warning (shown below) of all fatal crashes with your app, so that you can respond as soon as possible.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/email.png"><img class="aligncenter size-medium wp-image-2054" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/email-241x300.png" alt="" width="241" height="300" /></a>

&nbsp;

Additionally, you can add Slack integration to Crashlytics, as well as Jira, BigQuery, and more. These integrations can be managed via the settings icon in the top left, then "Project settings", and the "Integrations" tab. Once this has been configured, you can receive instant notifications of all crashes (or only fatal issues) in your chosen Slack channel. This can be extremely useful for teams that tend to not monitor their email too closely, as it means any issue can immediately be investigated.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/slack.png"><img class="aligncenter size-full wp-image-2056" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/slack.png" alt="" width="649" height="391" /></a>
<h3>Additional Crashlytics functionality</h3>
Crashlytics' core functionality of bug reporting is very powerful by itself, but it can be improved with additional functionality. For example, all new issues can be responded to in <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/">Firebase Cloud Functions</a> via the <code>crashlytics.issue().onNew()</code> trigger. Additionally, regressed (<code>onRegressed()</code>) and large issue volume increases (<code>onVelocityAlert()</code>) are available to listen to. An <a href="https://firebase.google.com/docs/crashlytics/extend-with-cloud-functions" target="_blank" rel="noopener">official Firebase guide is available for this</a>.

To allow Crashlytics to <a href="https://firebase.google.com/docs/crashlytics/get-deobfuscated-reports" target="_blank" rel="noopener">automatically deobfuscate</a> your ProGuarded stack traces, make sure to remove <code>-printmapping mapping.txt</code> from your ProGuard config, and add:
<pre class="prettyprint"><span class="pun">-</span><span class="pln">keepattributes </span><span class="pun">*</span><span class="typ">Annotation</span><span class="pun">*</span><span class="no-select"><span class="pln">                      </span><span class="com">// Keep Crashlytics annotations</span></span>
<span class="pun">-</span><span class="pln">keepattributes </span><span class="typ">SourceFile</span><span class="pun">,</span><span class="typ">LineNumberTable</span><span class="no-select"><span class="pln">        </span><span class="com">// Keep file names/line numbers</span></span>
<span class="pun">-</span><span class="pln">keep </span><span class="kwd">public</span> <span class="kwd">class</span> <span class="pun">*</span> <span class="kwd">extends</span><span class="pln"> java</span><span class="pun">.</span><span class="pln">lang</span><span class="pun">.</span><span class="typ">Exception  // Keep custom exceptions (opt)</span></pre>
<h2>Web interface</h2>
Crashlytics' web interface is a dashboard showing a record of all recent crashes, and how many users are encountering them. A summary of all encountered crashes is also shown.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/overview.png"><img class="aligncenter size-large wp-image-2058" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/overview-1024x594.png" alt="" width="730" height="423" /></a>

The rest of the page is dedicated to listing all encountered crashes. Clicking any of these will provide metadata such as app versions that are crashing, devices encountering it, and detailed information about the crash. This detailed information consists of a full stack trace, information about the user's devices, and any custom logs / data that has been added.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/stacktrace.png"><img class="aligncenter size-large wp-image-2059" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/stacktrace-1024x599.png" alt="" width="730" height="427" /></a>
<h2>Conclusion</h2>
Firebase Crashlytics is an extremely powerful automatic crash reporter, and is unlikely to be surpassed by any alternatives. Due to the unlimited free usage, simple integration, and ability to integrate with third party systems, it's hard to see any major drawbacks. In 2015, it was used in <a href="https://fabric.io/blog/2015/05/20/just-in-crashlytics-1-in-performance-answers-2-in-mobile-analytics">42% of the top 200 apps</a>, and this figure has only grown since then. As such, the system has a very reliable history, and should almost always be the choice for crash monitoring.

It's worth noting that the product has a rather complex history. Unlike most Firebase services which are developed in-house by Google, Crashlytics was originally a start-up (2011), then owned by Twitter (2013), then became a part of Fabric (2014), and is now owned by Google (2017)! The final transition is due to Fabric shutting down, with support ending in mid-2019. Migration is simple with <a href="https://get.fabric.io/roadmap" target="_blank" rel="noopener">Fabric / Google's guide</a>, hopefully Crashlytics has a permanent home now!

Previous: <a style="transition-property: all;" href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-ml-kit/">Developing An</a><a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-ml-kit/">droid Apps With Firebase ML Kit</a>