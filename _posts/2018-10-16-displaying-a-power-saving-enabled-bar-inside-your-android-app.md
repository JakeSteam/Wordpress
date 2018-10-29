---
ID: 1766
post_title: 'Displaying a &#8220;Power Saving Enabled&#8221; Bar Inside Your Android App'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/displaying-a-power-saving-enabled-bar-inside-your-android-app/
published: true
post_date: 2018-10-16 17:56:59
---
If your app uses Android hardware features or otherwise needs to be running most of the time, tools such as power saving mode can limit your app's freedom. Whilst the mode is great for users, the inconsistent implementation (much better now that Doze mode exists, and power saving is more standardised) often leads to unreliable performance on certain devices. Making users aware of this can prevent giving users a negative impression of your app.

In this tutorial, a simple animated "Power saving mode enabled" bar will be displayed inside the app whilst there any power saving mode is enabled, and disappear when it is disabled. An <a href="https://github.com/JakeSteam/PowerSavingBarDemo" target="_blank">example power saving bar project</a> is available, as well as a <a href="https://www.youtube.com/watch?v=AbYpf-pjPxI" target="_blank">video of the finished implementation</a>.

<!--more-->

Please note that this <b>only works on Lollipop and above</b>, the APIs needed to safely get power saving status are not available before then.

This tutorial also shares most of it's code with <a href="https://blog.jakelee.co.uk//displaying-a-no-internet-bar-inside-your-android-app/">an earlier post about displaying a "No Internet" bar</a> in a similar way.
<h2>Add bar to layout</h2>
First, the bar has to actually be added to the layout. This can of course be customised easily, the demo project uses a bright orange bar to alert the user.
<pre>&lt;TextView
android:id="@+id/power_saving_bar"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:background="#F28200"
android:gravity="center_horizontal"
android:padding="5dp"
android:text="Power saving mode is on, this app's super duper features may not work correctly!"
android:textColor="#FFFFFF"
android:visibility="gone"
app:layout_constraintBottom_toBottomOf="parent" /&gt;
</pre>
<h2>Setup power saving listener</h2>
Next, a <code>BroadcastReceiver</code> needs to be created to catch the power saving toggle events broadcast by the operating system. The <code>IntentFilter</code> ensures that only the correct events are received.
<pre>@RequiresApi(Build.VERSION_CODES.LOLLIPOP)
private fun setupPowerSavingReceiver() {
togglePowerSavingBar((getSystemService(Context.POWER_SERVICE) as PowerManager).isPowerSaveMode)
powerSavingReceiver = object : BroadcastReceiver() {
override fun onReceive(context: Context, intent: Intent) {
val powerManager: PowerManager =
context.getSystemService(Context.POWER_SERVICE) as PowerManager
togglePowerSavingBar(powerManager.isPowerSaveMode)
Log.e("PowerSaving", "Enabled: ${powerManager.isPowerSaveMode}")
}
}
registerReceiver(
powerSavingReceiver,
IntentFilter("android.os.action.POWER_SAVE_MODE_CHANGED")
)
}
</pre>
<h2>Hook into Android lifecycle events</h2>
Next, we need to make sure receivers are only registered whilst our activity is, otherwise we may continue listening to power saving events even after the app is closed. <code>onStart</code> and <code>onStop</code> should be used for registering and unregistering the receiver.
<pre>private var powerSavingReceiver: BroadcastReceiver? = null

override fun onStart() {
super.onStart()
if (Build.VERSION.SDK_INT &gt;= Build.VERSION_CODES.LOLLIPOP) {
setupPowerSavingReceiver()
}
}

override fun onStop() {
super.onStop()
powerSavingReceiver?.let { unregisterReceiver(it) }
}
</pre>
<h2>Display bar</h2>
Finally, and most importantly, we need to actually show the bar! <code>togglePowerSavingMode</code> is called when the power saving mode is enabled or disabled, and accepts a boolean determining if the bar should be shown or hidden. Two simple animations are used to improve appearances:
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
&lt;/set&gt;
</pre>
</li>
 	<li><code>exit_to_bottom.xml</code> is used for the bar disappearing:
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;set xmlns:android="http://schemas.android.com/apk/res/android"
android:shareInterpolator="false"&gt;
&lt;translate
android:duration="400"
android:fromXDelta="0%"
android:fromYDelta="0%"
android:toXDelta="0%"
android:toYDelta="100%" /&gt;
&lt;/set&gt;
</pre>
</li>
</ol>
Finally, these animations are used to toggle visibility:
<pre>private fun togglePowerSavingBar(display: Boolean) {
if (display &amp;&amp; power_saving_bar.visibility != View.VISIBLE) {
val enterAnim = AnimationUtils.loadAnimation(this, R.anim.enter_from_bottom)
power_saving_bar.startAnimation(enterAnim)
} else if (!display &amp;&amp; power_saving_bar.visibility != View.GONE) {
val exitAnim = AnimationUtils.loadAnimation(this, R.anim.exit_to_bottom)
power_saving_bar.startAnimation(exitAnim)
}
power_saving_bar.visibility = if (display) View.VISIBLE else View.GONE
}
</pre>
<h2>Conclusion</h2>
The main drawback of this simple solution is the requirement for Android Lollipop. Luckily, on older devices the functionality simply won't be enabled, so users won't actually be aware they are missing out.

Additionally, during testing I discovered Sony's "STAMINA MODE" isn't implemented in the same standardised way as other manufacturers, meaning it cannot be listened to in the same way. A partial workaround is available, by checking Sony device's settings when the app starts (or <code>onResume</code>):
<pre>private fun performSonyPowerSavingCheck() {
if (Build.MANUFACTURER.toUpperCase() == "SONY") {
togglePowerSavingBar(
Settings.Secure.getInt(contentResolver, "somc.stamina_mode", 0) == 1
)
}
}
</pre>
This workaround isn't perfect, as it only tells you if the user has STAMINA MODE enabled, not whether it is currently active. For example, the user may have it set to activate at 20% battery, and currently be at 100%, yet the check will still return true. As such, consider whether it is worth implementing for Sony devices.

An <a href="https://github.com/JakeSteam/PowerSavingBarDemo" target="_blank">example power saving bar project</a> has also been created for this tutorial.