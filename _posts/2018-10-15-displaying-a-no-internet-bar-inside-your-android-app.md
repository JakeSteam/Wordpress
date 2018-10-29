---
ID: 1763
post_title: 'Displaying a &#8220;No Internet&#8221; Bar Inside Your Android App'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/displaying-a-no-internet-bar-inside-your-android-app/
published: true
post_date: 2018-10-15 19:20:18
---
Whilst developing an app, you're likely to have a strong, reliable data connection at all times. In the real world however, users often will be without connectivity, and your app should react appropriately.

In this tutorial, a simple animated "No internet is available" bar will be displayed inside the app whilst there is no internet, and disappear when connectivity returns. An <a href="https://github.com/JakeSteam/WarningBarDemo" target="_blank" rel="noopener">example no internet bar project</a> is available, as well as a <a href="https://www.youtube.com/watch?v=refrQSsaiyc" target="_blank" rel="noopener">video of the finished implementation</a>.

<!--more-->
<h2>Add network state permission</h2>
First, permission to access the network state is needed. This permission doesn't need to be requested at runtime, and just being included in the <code>AndroidManifest.xml</code> is sufficient:
<pre>&lt;uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" /&gt;</pre>
<h2>Add bar to layout</h2>
Next, the bar has to actually be added to the layout. This can of course be customised easily, I've gone for a solid bright pink bar to grab attention!
<pre>    &lt;TextView
        android:id="@+id/no_internet_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#FF4081"
        android:gravity="center_horizontal"
        android:padding="5dp"
        android:text="There is no internet connection available!"
        android:textColor="#FFFFFF"
        android:visibility="gone"
        app:layout_constraintBottom_toBottomOf="parent" /&gt;</pre>
<h2>Create network receiver</h2>
<code>NetworkReceiver.kt</code> is a small class that serves 3 functions:
<ol>
 	<li>Checking the current internet status on demand:
<pre>    private fun hasInternet(context: Context): Boolean {
        val connectMgr = (context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager)
        val activeNetworkInfo = connectMgr.activeNetworkInfo
        return activeNetworkInfo != null &amp;amp;amp;&amp;amp;amp; activeNetworkInfo.isConnected
    }</pre>
</li>
 	<li>Providing an interface for activities to implement, so that it can broadcast network state change events:
<pre>    interface NetworkReceiverListener {
        fun onNetworkConnectionChanged(isConnected: Boolean)
    }

    companion object {
        var networkReceiverListener: NetworkReceiverListener? = null
    }</pre>
</li>
 	<li>Actually broadcasting the events as they are received:
<pre>    override fun onReceive(context: Context, arg1: Intent) {
        if (networkReceiverListener != null) {
            networkReceiverListener!!.onNetworkConnectionChanged(hasInternet(context))
        }
    }</pre>
</li>
</ol>
<h2>Setup network listener</h2>
Initialising the <code>NetworkReceiver</code>'s <code>networkReceiverListener</code> hooks the activity into the broadcast receiver we just created. This should be done during <code>onCreate()</code> or as early as possible:
<pre>    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        setupNetworkListener()
    }

    private fun setupNetworkListener() {
        NetworkReceiver.networkReceiverListener = object : NetworkReceiver.NetworkReceiverListener {
            override fun onNetworkConnectionChanged(isConnected: Boolean) {
                toggleNoInternetBar(!isConnected)
            }
        }
    }</pre>
<h2>Hook into Android lifecycle events</h2>
Next, we need to make sure receivers are only registered whilst our activity is, otherwise we may continue listening to network events even after the app is closed. <code>onStart</code> and <code>onStop</code> should be used for registering and unregistering the receiver.
<pre>    private var networkReceiver: NetworkReceiver? = null

    override fun onStart() {
        super.onStart()
        networkReceiver = NetworkReceiver()
        registerReceiver(networkReceiver, IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION))
    }

    override fun onStop() {
        super.onStop()
        networkReceiver?.let { unregisterReceiver(it) }
    }</pre>
<h2>Display bar</h2>
Finally, and most importantly, we need to actually show the bar! <code>toggleNoInternetBar</code> is called when the network state changes, and accepts a boolean determining if the bar should be shown or hidden. Two simple animations are used to improve appearances:
<ol>
 	<li><code>enter_from_bottom.xml</code> is used for the bar appearing:
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false"&gt;
    &lt;translate
        android:duration="400"
        android:fromXDelta="0%"
        android:fromYDelta="100%"
        android:toXDelta="0%"
        android:toYDelta="0%" /&gt;
&lt;/set&gt;</pre>
</li>
 	<li><code>exit_to_bottom.xml</code> is used for the bar disappearing:
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false"&gt;
    &lt;translate
        android:duration="400"
        android:fromXDelta="0%"
        android:fromYDelta="0%"
        android:toXDelta="0%"
        android:toYDelta="100%" /&gt;
&lt;/set&gt;</pre>
</li>
</ol>
Finally, these animations are used to toggle visibility:
<pre>    private fun toggleNoInternetBar(display: Boolean) {
        if (display) {
            val enterAnim = AnimationUtils.loadAnimation(this, R.anim.enter_from_bottom)
            no_internet_bar.startAnimation(enterAnim)
        } else {
            val exitAnim = AnimationUtils.loadAnimation(this, R.anim.exit_to_bottom)
            no_internet_bar.startAnimation(exitAnim)
        }
        no_internet_bar.visibility = if (display) View.VISIBLE else View.GONE
    }</pre>
<h2>Conclusion</h2>
For an internet-dependent app, the ability to show users a warning that the displayed data may not be up to date is an easy way to improve the user experience. Small features like this really add up, and can help persuade a user to feel positively towards your app / service.

One potentially unexpected quirk of the implementation in this tutorial that can cause issues during testing is the delay when disabling Airplane Mode. Once Airplane Mode is disabled, it can take 5-10 seconds until connectivity returns and the bar disappears.

An <a href="https://github.com/JakeSteam/WarningBarDemo" target="_blank" rel="noopener">example no internet bar project</a> has also been created for this tutorial.