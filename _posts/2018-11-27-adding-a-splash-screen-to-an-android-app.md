---
ID: 2069
post_title: Adding A Splash Screen To An Android App
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/adding-a-splash-screen-to-an-android-app/
published: true
post_date: 2018-11-27 18:00:23
---
Often, an app may take a few seconds to start-up, especially the first time. A plain white screen is shown by default, luckily this can be customised relatively easily! The solution is to add a splash screen to your app, whilst being careful to avoid increasing the app's load time.

Many naïve splash screens actually make the app take longer to load, but introducing an artificial delay. The approach in this tutorial doesn't increase load time, and only displays as long as the app loads. An <a href="https://github.com/JakeSteam/SplashScreenDemo">example implementation is available</a>.

<!--more-->

First, add a design for the splash screen and name it <code>splash_screen_background.xml</code> inside <code>/drawable/</code>. For this example, a new drawable <code>ic_time</code> was created and layered on top of the default <code>colorPrimary</code>.
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android" android:opacity="opaque"&gt;
    &lt;item android:drawable="@color/colorPrimary" /&gt;
    &lt;item&gt;
        &lt;bitmap
                android:gravity="center"
                android:scaleType="centerInside"
                android:src="@drawable/ic_time" /&gt;
    &lt;/item&gt;
&lt;/layer-list&gt;</pre>
Unfortunately, this can't be a normal XML layout, and must instead be an XML drawable. Layer lists are used for the most common splash screens; coloured background with an icon in the middle. This may take some experimentation, as scaling the image correctly can be tricky.

Next, add a new style <code>SplashScreen</code> to your <code>styles.xml</code>, this is the style for the splash screen and uses your newly created background:
<pre>&lt;style name="SplashScreen" parent="Theme.AppCompat.NoActionBar"&gt;
    &lt;item name="android:windowBackground"&gt;@drawable/splash_screen_background&lt;/item&gt;
&lt;/style&gt;</pre>
Now set your starting activity (e.g. <code>MainActivity</code>) to use your <code>SplashScreen</code> style inside <code>AndroidManifest.xml</code>:
<pre>&lt;activity
        android:name=".MainActivity"
        android:theme="@style/SplashScreen"&gt;</pre>
Finally, call <code>setTheme(R.style.AppTheme)</code> at the start of your <code>MainActivity</code>'s <code>onCreate</code>, e.g.:
<pre>override fun onCreate(savedInstanceState: Bundle?) {
    setTheme(R.style.AppTheme)
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
}</pre>
Your application will now use the activity's default splash screen theme until <code>onCreate</code> is called, when it will load the normal theme!

For testing, it's often handy to add a <code>sleep(5000)</code> before setting the theme on <code>onCreate</code>, otherwise the app loads too quickly to get a long look at the splash screen. Drawables inside your splash screen background should also be non-vector images, as parsing the vectors often causes unexpected effects.

Full implementation source code: <a href="https://github.com/JakeSteam/SplashScreenDemo">https://github.com/JakeSteam/SplashScreenDemo</a>

&nbsp;