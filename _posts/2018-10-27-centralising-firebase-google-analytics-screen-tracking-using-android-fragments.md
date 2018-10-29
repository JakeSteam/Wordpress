---
ID: 1802
post_title: >
  Centralising Firebase / Google Analytics
  Screen Tracking Using Android Fragments
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/centralising-firebase-google-analytics-screen-tracking-using-android-fragments/
published: true
post_date: 2018-10-27 07:00:02
---
Whilst tracking user's screen views with Firebase / Google Analytics can be very simple to setup, it can easily result in a very messy codebase, with hardcoded strings all over the place. Keeping all of the tracking logic in one place allows an instant overview of all tracked screens, as well as easily checking where a screen is reporting a view. Note that this tutorial is for fragments, activities can already be tracked easily automatically.

This guide on how to create a <code>TrackingUtil()</code> class is also <a href="https://gist.github.com/JakeSteam/19c4d4869001b41c81de9c5b91dfd4c3" target="_blank" rel="noopener">available as a Gist</a>. It also assumes Analytics has already been setup!

<!--more-->

In our <code>TrackingUtil</code> class, we're going to achieve two goals. Centralise all screen name definitions, and provide a simple interface to report views via.
<h2>Centralised screen name definition</h2>
An enum is perfect for this, as it can easily be referenced in other classes, whilst maintaining a static name without needing to use strings.
<pre>class TrackingUtil(val context: Context) {
    enum class Screens {
        ChangePassword,
        Contact,
        Dashboard,
        DashboardInfo,
        ForgottenPassword,
        Info,
        Login,
        Registration
    }</pre>
Now, the login fragment / activity can use <code>TrackingUtil.Login</code> if it wishes to log a screen view.
<h2>Reporting interface</h2>
A simple wrapper will be used for this, without any modification to the incoming screen views. In this example of filtering reported screen views, no data is sent when in debug mode.
<pre>
    fun track(screen: Screens) {
        if (!BuildConfig.DEBUG) {
            FirebaseAnalytics.getInstance(context.applicationContext)
                .setCurrentScreen(context, screen.name, null)
            Timber.d("Sending screen view of ${screen.name}")
        }
    }</pre>
Alternatively, <a href="https://blog.jakelee.co.uk//filtering-google-firebase-analytics-traffic-by-buildtype-environment-on-android">user properties can be used to filter environment data</a>.
<h2>Using TrackingUtil</h2>
Now, just call <code>.track</code> on an instance of <code>TrackingUtil</code> and pass a screen from our earlier enum to send a screen view. If using fragments, the best place is inside <code>onCreateView</code>:
<pre>
    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        TrackingUtil(activity!!).track(TrackingUtil.Screens.Login)
        return inflater.inflate(R.layout.fragment_login, container, false)
    }</pre>
Whilst this solution does require access to a context, the core Firebase Analytics call does too, so this is no more trouble than using the original reporting method.

Performing "Find Usages" on any screen in the enum provides a quick way to check the correct places are calling each screen, and any unused screen name will be immediately obvious due to the grey colour caused by an unused resource.
<h2>Viewing screen view data</h2>
On the main Analytics dashboard, the user engagement widget provides a link to "View screen_view event details", which will take you to an overview of where users have been in your app.

Next, selecting "Screen name" in the "User engagement" widget will show where users have been in your app, broken down by percentage and average time.
<img class="alignnone size-full wp-image-1803" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/axtxt2h.png" alt="axtxt2h" width="506" height="342" />

This data on a per-screen basis should help analyse and improve user's experiences in your app, and tell you the areas people care most about. Even if there is no current goal to track and analyse this data, it's worth tracking in case it's needed in the future.