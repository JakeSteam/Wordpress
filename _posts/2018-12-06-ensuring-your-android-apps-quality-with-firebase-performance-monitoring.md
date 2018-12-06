---
ID: 2116
post_title: 'Ensuring Your Android App&#8217;s Quality With Firebase Performance Monitoring'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/ensuring-your-android-apps-quality-with-firebase-performance-monitoring/
published: true
post_date: 2018-12-06 19:59:37
---
Firebase Performance Monitoring allows you to gather automatic performance data about your app, as well as performing manual performance traces for later analysis. All of these contain aggregated information about user's devices, meaning long-term issues can be quickly identified and resolved.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you’ll need access to the <a href="https://console.firebase.google.com/u/0/project/_/performance" target="_blank" rel="noopener">Firebase Performance Monitoring dashboard</a>, and the <a href="https://firebase.google.com/docs/perf-mon/get-started-android" target="_blank" rel="noopener">official documentation</a> may be useful.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/11" target="_blank" rel="noopener">pull request for adding Firebase Performance Monitoring</a> if you just want to see the code changes required. This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.

In this tutorial, you'll learn how to monitor the performance of your app, as well as benchmark specific scenarios across all users. You'll also be able to track network requests, startup time, and how long your app was in the foreground.
<h3>Adding Performance Monitoring to your project</h3>
First, in your app-level <code>build.gradle</code>, just below <code>apply plugin: 'com.android.application'</code> add:
<pre>apply <span class="pl-c1">plugin</span>: <span class="pl-s"><span class="pl-pds">'</span>com.google.firebase.firebase-perf<span class="pl-pds">'
</span></span></pre>
Still in the same file, add <code>implementation 'com.google.firebase:firebase-perf:16.2.0'</code> <span class="pl-s"><span class="pl-pds">to your dependencies.</span></span>

Next, in your project-level <code>build.gradle</code>, in the <code>buildscript</code> -&gt; <code>repositories</code> section, make sure <code>jcenter()</code> is listed.

Finally, still in the same file, add the following inside <code>buildscript</code> -&gt; <code>repositories</code>:
<pre>classpath 'com.google.firebase:firebase-plugins:1.1.5'</pre>
Perform a Gradle Sync, and your app's performance + network requests are now being monitored!
<h3>Performing manual traces</h3>
Whilst app startup time, activity time, app background, and app foreground times are all measured automatically, you may wish to add your own time tracking.

This can be done in 2 ways:

First, specifying methods to always track via annotations. This is useful if you want to measure entire methods, don't want to track custom metrics, and want to keep your actual functions short and clean. This is done by adding <code>@AddTrace(name = "yourTraceName")</code> before a method. For example, this function's time is tracked under the trace name "automatic":
<pre>@AddTrace(name = "automatic")
private fun performAutomaticTrace() {
    for (i in 1..(1..20).shuffled().last()) {
        Log.d("Performance", "Value is $i")
        sleep(100)
    }
}</pre>
The second method is manually starting a trace. This is used when you wish to modify the trace's metadata whilst it is running, such as modifying custom attributes being tracked. These manual traces are started by creating a trace using <code>FirebasePerformance.getInstance().newTrace("yourTraceName")</code>, then starting it with <code>.start()</code>. The trace can then be stopped with <code>.stop()</code>. For example, this function's time is tracked under the trace name "manual":
<pre>private fun performManualTrace() {
    val trace = FirebasePerformance.getInstance().newTrace("manual")
    trace.start()
    for (i in 1..(1..10).shuffled().last()) {
        sleep(100)
    }
    trace.stop()
}</pre>
<h3>Monitor custom attributes</h3>
In addition to monitoring overall performance, you can also set and modify up to 5 custom attributes per trace. This can be useful for logging which code version an A/B tester is on. It can also be useful if you want to track how many times a user performs a specific action during the trace.

You must use the second method listed above (<code>.newTrace()</code>) to use custom attributes, as you need a reference to a trace. Once you have your reference, you can set a value using <code>putAttribute(name, value)</code>:
<pre>trace.putAttribute("run_manual", true.toString())</pre>
This value can also be retrieved with <code>trace.getAttribute(name)</code> and removed with <code>trace.removeAttribute(name)</code>.

Note that attributes are always stored as strings. However, if you wish to store numerical values you can use <code>trace.incrementMetric(name, amount)</code>. This value can be retrieved with <code>trace.getLongMetric(name)</code>, but cannot be deleted.
<h2>Web interface</h2>
<h3>Dashboard</h3>
The dashboard provides an overview of any emerging issues that require attention, as well as any poorly performing screens and other important metrics.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-issue-list.png"><img class="aligncenter wp-image-2117 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-issue-list.png" alt="" width="960" height="333" /></a>

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-more.png"><img class="aligncenter wp-image-2118 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-more.png" alt="" width="947" height="720" /></a>

Clicking an issue will provide more information about it (similar to <a href="https://blog.jakelee.co.uk/ensuring-your-android-apps-quality-with-firebase-crashlytics/">Crashlytics</a>), as well as the option to close or mute the issue.
<h3>On device</h3>
The on device tab provides an overview of trace durations, both manual and automatic. Clicking any will provide a more detailed overview, as well as the option to view extremely detailed data for a specific session. This data is extremely useful when diagnosing issues like a memory leak or sluggish performance.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-sessions-further.png"><img class="alignleft size-medium wp-image-2121" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-sessions-further-300x223.png" alt="" width="300" height="223" /></a><a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-sessions.png"><img class="size-medium wp-image-2120 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-sessions-300x179.png" alt="" width="300" height="179" /></a>
<h3>Network</h3>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-network.png"><img class="aligncenter size-full wp-image-2124" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/perf-mon-network.png" alt="" width="996" height="268" /></a>

This tab shows information about network requests included in your traces, and is covered in detail in the <a href="https://firebase.google.com/docs/perf-mon/get-started-android#manual-network">official Firebase docs</a>.
<h2>Conclusion</h2>
Whilst there is a slight risk of a performance overhead being added (somewhat ironically) when performing traces, this is usually outweighed by the benefits gained from knowing exactly how your app is performing. For example, if the median app start time has doubled after an update, without Firebase Performance Monitoring there wouldn't be any way to know this! You could then track individual session and identify exactly what has changed since an earlier version.

Note that there are a few limitations with this method of performance monitoring. Most notably, only the main process can be monitored, meaning apps that make heavy use of threads or RxJava may end up with inaccurate reporting. Despite this, the service is free for unlimited usage, so is definitely worth including from the start on all apps. The collected data is then useful when attempting to optimise performance at a later date.

Previous: <a href="https://blog.jakelee.co.uk/ensuring-your-android-apps-quality-with-firebase-crashlytics/">Ensuring Your Android App's Quality With Firebase Crashlytics</a>

<span style="font-size: 12pt;"><em>Thanks to <a href="https://firebase.google.com/docs/">Firebase docs</a> for some of the images used in this article.</em></span>