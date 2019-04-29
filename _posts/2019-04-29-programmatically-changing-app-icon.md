---
ID: 2478
post_title: >
  How to programmatically change your
  Android app icon and name
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/programmatically-changing-app-icon/
published: true
post_date: 2019-04-29 15:00:21
---
In Android, your app's icon is a crucial part of your project. It's what grabs the user's attention first, identifies your app amongst the sea of competitors, and is the most important piece of visual branding. However, usually this icon is defined in your manifest, then never changes except through update.

Recently, out of curiosity, I discovered it was possible to change the app icon / name whenever you want, without an update. Luckily, it's pretty straightforward! Whilst this article is a detailed explanation of the solution, it is also available <a href="https://gist.github.com/JakeSteam/5efffeee23097d8141a69e3c74649a2f" target="_blank" rel="noopener noreferrer">as a Gist</a>, and <a href="https://github.com/JakeSteam/DynamicIconChanging" target="_blank" rel="noopener noreferrer">an example project</a>.<!--more-->
<h2>Approach</h2>
The <code>android.intent.action.MAIN</code> action (which usually exists on your default activity) tells Android which activity should be used to start your app. However, we can define multiple aliases for our single activity, with the end result of multiple configurations of icon + title for our app.

All of our activity aliases will define themselves as <strong>the</strong> starting point to the app, so we'll need to disable all the ones not currently in use. This will result in the OS only showing the remaining activated alias on the app launcher. Note that this can take a few seconds to take effect, and almost certainly has strange side effects!
<h2>Registering aliases in manifest</h2>
First, <strong>remove</strong> the <code>MAIN</code> action (<code>&lt;action android:name="android.intent.action.MAIN" /&gt;</code>) from your app's launcher activity (by default <code>MainActivity</code>). This is so our aliases can use it instead. Your activity should end up something like this:
<pre>&lt;activity android:name=".MainActivity"&gt;
    &lt;intent-filter&gt;
        &lt;category android:name="android.intent.category.LAUNCHER"/&gt;
    &lt;/intent-filter&gt;
&lt;/activity&gt;</pre>
Next, add an <code>&lt;activity-alias&gt;</code> for every different icon / name you want your app to use. There are a few required fields:
<ul>
 	<li><code>label</code> for the app's display name</li>
 	<li><code>icon</code> for the app's icon. This can be a drawable or mipmap, with mipmap recommended.</li>
 	<li><code>name</code> can be any valid string (no special characters, spaces, etc), and is used to reference this alias later on. In this example I've used the colour of the icon.</li>
 	<li><code>targetActivity</code> must be your existing launcher activity.</li>
 	<li><code>enabled</code> must be set to true for at least one of your <code>activity-alias</code>. Otherwise, your app will not be able to launch.</li>
</ul>
<pre>&lt;activity-alias
        android:label="Red app"
        android:icon="@mipmap/icon_red"
        android:name=".RED"
        android:enabled="true"
        android:targetActivity=".MainActivity"&gt;
    &lt;intent-filter&gt;
        &lt;action android:name="android.intent.action.MAIN" /&gt;
        &lt;category android:name="android.intent.category.LAUNCHER" /&gt;
    &lt;/intent-filter&gt;
&lt;/activity-alias&gt;</pre>
<h2>Icon switching</h2>
To enable one of our aliases, we also need to disable all others. The core piece of code used for this is below, with <code>PackageManager.COMPONENT_ENABLED_STATE_ENABLED</code> changed to <code>...STATE_DISABLED</code> for the unwanted aliases:
<pre>getPackageManager().setComponentEnabledSetting(
    new ComponentName(BuildConfig.APPLICATION_ID, "com.example.package.RED"), 
    PackageManager.COMPONENT_ENABLED_STATE_ENABLED, PackageManager.DONT_KILL_APP
);</pre>
The <code>BuildConfig.APPLICATION_ID</code> is used to tell the package manager which package you want to modify a component of. <code>"com.example.package.RED"</code> should be your package name, then the <code>activity-alias</code>' <code>name</code> defined in the previous step. If you're using Kotlin, the easiest way to set this is using inline string formatting, e.g:
<pre>"${BuildConfig.APPLICATION_ID}.$aliasName"</pre>
<h2>Further usage</h2>
A good way to ensure all other activity aliases are disabled when one is enabled is to use a helper function along with an enum. This enum should contain every possible activity alias. Then, loop over this enum and set all values to the necessary state:
<pre>enum class ICON_COLOUR { RED, GREEN, BLUE }

private fun setIcon(targetColour: ICON_COLOUR) {
    for (value in ICON_COLOUR.values()) {
        val action = if (value == targetColour) {
            PackageManager.COMPONENT_ENABLED_STATE_ENABLED
        } else {
            PackageManager.COMPONENT_ENABLED_STATE_DISABLED
        }
        packageManager.setComponentEnabledSetting(
            ComponentName(BuildConfig.APPLICATION_ID, "${BuildConfig.APPLICATION_ID}.${value.name}"),
            action, PackageManager.DONT_KILL_APP
        )
    }
}</pre>
App icons can then be changed with just <code>setIcon(ICON_COLOUR.RED)</code>. Easy!
<h2>Notes</h2>
I noticed a few quirks during my experimenting with this, and I'd be hesitant to use it in a production app without extensive testing. A couple of notable issues:
<ul>
 	<li>If you programmatically change your alias, it can take a few seconds for the launcher to update.</li>
 	<li>Related to the previous issue, if you try to open the app via the icon during these few seconds, there will be a mismatch between the launcher shortcut and the manifest. The OS will then be unable to open your app, and display an <code>App not found</code> toast.</li>
 	<li>If you are trying to deploy an app that has had it's alias changed since install, Android Studio will fail to launch it. It will need to be uninstalled or reset back to the default.</li>
 	<li>The original title and icon will show in many places throughout the OS. For example:
<ul>
 	<li>Task switcher</li>
 	<li>Applications list</li>
</ul>
</li>
 	<li>Non-default launchers may not display this changed icon.</li>
</ul>
Special thanks to this <a href="https://stackoverflow.com/a/15249542/608312" target="_blank" rel="noopener noreferrer">StackOverflow answer by P-A</a> for the core code. Note that the accepted answer on that question will not work from Marshmallow onwards, as it requires dangerous permissions (add / remove shortcuts), and doesn't perform as reliably anyway. As such, the answer linked to is the one used for this post.
<h2>Conclusion</h2>
In conclusion, yes it is possible to change the app icon. However, I wouldn't rely on this at all, and would personally stick to just changing it with an app update in almost all cases.

That being said, there's definitely a lot of potential to this very underused technique. For example, a game where the app icon changes colour whilst it's the player's turn, or during a timed event. In this context, it's like a very limited notification, that the user can't even turn off! I'd be interested to see which games / apps use this feature in the wild, and if they've experienced any side effects.