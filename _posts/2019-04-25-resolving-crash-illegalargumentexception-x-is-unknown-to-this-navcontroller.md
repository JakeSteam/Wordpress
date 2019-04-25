---
ID: 2470
post_title: 'Resolving crash &#8220;IllegalArgumentException x is unknown to this NavController&#8221;'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/resolving-crash-illegalargumentexception-x-is-unknown-to-this-navcontroller/
published: true
post_date: 2019-04-25 14:54:27
---
Google's <a href="https://developer.android.com/guide/navigation/navigation-getting-started" target="_blank" rel="noopener noreferrer">AndroidX navigation libraries</a> are undoubtedly extremely useful, however they have a few quirks. For example, the following stack trace recently started showing up in my <a href="https://blog.jakelee.co.uk/ensuring-your-android-apps-quality-with-firebase-crashlytics/" target="_blank" rel="noopener noreferrer">Crashlytics crash logs</a>:
<pre>Fatal Exception: java.lang.IllegalArgumentException
navigation destination com.example.myapp:id/myActionId is unknown to this NavController</pre>
I couldn't figure out how it was happening, as all other logging seemed normal. It was happening very rarely on a wide variety of devices, and the root cause was a little surprising. When navigating to another fragment using <code>myNavController.navigate(<span class="pl-en">R</span>.id.myFragment, bundle)</code>, a crash happens if two navigation events are triggered close together. This often happens if you have a list of links, and the user (accidentally) presses on two at once.<!--more-->

This is caused by the navigation controller changing the user's location in the app to the destination immediately. This means the second navigation click can't find the navigation action, as the action is not available on the destination fragment. For example, if a fragment has nav graph actions to go to SubFragmentA and SubFragmentB, pressing both links at once will result in:
<ol>
 	<li>First, NavController receives SubFragmentA navigation event whilst on Fragment1.</li>
 	<li>The NavController looks up navigation event on Fragment1, and finds it.</li>
 	<li>Then the NavController recognizes that the new location is SubFragmentA and starts playing any transitions.</li>
 	<li>Next, NavController receives SubFragmentB navigation event whilst on SubFragmentA.</li>
 	<li>Then the NavController tries to look up navigation event on SubFragmentA, and fails to find it.</li>
 	<li>Crash!</li>
</ol>
There are 2 easy solutions to this. The first is preferred, but not always possible.
<h2>Checking current location</h2>
The first solution is to check the current location is correct before acting on an action. This will stop the above process before #4. The downside of this is needing to always know the layout of your app, and being unable to reuse navigation actions easily from other fragments. However, for a simple app / use case this will instantly resolve the issue:
<pre>if (it.findNavController().currentDestination?.id == R.id.fragment_dashboard) {
    it.findNavController().navigate(R.id.toDetails)
}</pre>
Note that <code>R.id.fragment_dashboard</code> is the nav graph ID of the current fragment, and <code>R.id.fragment_detail</code> is the nav graph ID of the target fragment.
<h2>Catching exception</h2>
The second solution is a lot less attractive, and involves simply catching the thrown exception. This lets the first navigation continue as expected, and ignores the second navigation.
<pre>try { 
    it.findNavController().navigate(R.id.toDetails)
} catch (e: IllegalArgumentException) {
    // User tried tapping 2 links at once!
    Timber.e("Can't open 2 links at once!")
}</pre>