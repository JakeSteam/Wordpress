---
ID: 1381
post_title: >
  Auto-detecting Device Orientation Whilst
  Allowing User to Override
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/auto-detecting-device-orientation-whilst-allowing-user-to-override/
published: true
post_date: 2017-06-13 11:20:01
---
<h2>The Problem</h2>
When creating games (and other apps), screen orientation is very important. In general, more casual games use portrait, whilst more hardcore / intense games use landscape. However, some games may be inbetween these two categories, or may wish to reach a wider audience by supporting both. Automatically rotating to match device orientation is easy, but allowing the user to "lock" one orientation is a little trickier.
<!--more-->
<h2>The Solution</h2>
First, we're going to store the user's current preference (auto, landscape, or portrait), then this is going to be applied whenever an activity resumes. It is applied on resume instead of on create, to ensure reusing / reentering activities does not cause the app to switch between orientations.

Now, ensure all the activities you want to be rotatable (almost certainly all) are not specifying <code>orientation</code> under <code>configChanges</code>. If it is specified, it tells the device that the app will handle orientation changes, when we actually want the device to do the hard work for us.

Next, implement the following into a BaseActivity and ensure all activities extend is (recommended), or implement it into each activity. <code>setRequestedOrientation()</code> informs the device which orientation we want to use, then the user's preference is loaded. The actual implementation of preference saving could just as easily be a <a href="https://developer.android.com/reference/android/content/SharedPreferences.html" target="_blank" rel="noopener">shared preference</a>, this implementation uses an ORM object.
<pre>@Override
protected void onResume() {
    super.onResume();

    //noinspection ResourceType
    setRequestedOrientation(Setting.get(Enums.Setting.Orientation).getIntValue());
}</pre>
Note that the <code>//noinspection ResourceType</code> is required as we are passing an integer value directly, instead of using a constant. Suppressing errors is generally bad practice, but in this case <a href="https://stackoverflow.com/questions/28557696/how-to-use-activityinfo-screenorientation" target="_blank" rel="noopener">it's unavoidable</a> due to the programmatic nature of the orientation.

The integers to use for orientations are available directly from <a href="https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/content/pm/ActivityInfo.java" target="_blank" rel="noopener">the Android source</a> (line 395+), but we only care about 3 in particular, which are specified as internal constants for simplicity. They're self explanatory, with 4 (auto) being the default.
<pre>public final static int ORIENTATION_AUTO = 4;
public final static int ORIENTATION_LANDSCAPE = 6;
public final static int ORIENTATION_PORTRAIT = 7;</pre>
Adding a dropdown or other selector to actually allow the user to pick whether they want auto / landscape / portrait is outside the scope of this article. However, very similar code was used in the "Selecting a Language" section of <a href="https://blog.jakelee.co.uk//implementing-a-locale-language-selector/" target="_blank" rel="noopener">the previous "Implementing A Locale / Language Selector" article</a>.

As a starting point, the code used to return the constant to save from the position picked on a dropdown consisting of Auto, Portrait, and Landscape, is as follows:
<pre>private int getOrientationValue(int position) {
    switch (position) {
        case 1: return Constants.ORIENTATION_LANDSCAPE;
        case 2: return Constants.ORIENTATION_PORTRAIT;
        default: return Constants.ORIENTATION_AUTO;
    }
}</pre>
<h2>The Conclusion</h2>
Whilst the actual code used in this article is very minimal, the overall effect is very user-friendly and powerful. An optional extension to this work would be adding orientation locks for all 4 possible screen orientations, but in this use case just landscape or portrait was sufficient. Note that the constant values still obey the device sensor, meaning if the device is turned upside down whilst landscape locked, it will flip 180° so that it is correctly orientated.

The majority of apps will likely be able to settle on just one target orientation depending on their audience, but for apps that require targeting both, adding a user override is extremely important. For games in particular, the user may want to play the game in portrait whilst lying on their side, something that would be impossible without an orientation lock as the game would rotate to match gravity.

The code used in this article is from the <a href="https://www.reddit.com/r/BlacksmithSlots/" target="_blank" rel="noopener">upcoming Blacksmith Slots game</a>, and as always the (albeit minimal) code for this article is <a href="https://gist.github.com/JakeSteam/b9503c46e65088e0e651dc75d9782979" target="_blank" rel="noopener">available as a Gist</a>.