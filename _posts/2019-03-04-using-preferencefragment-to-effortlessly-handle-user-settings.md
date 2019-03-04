---
ID: 2411
post_title: >
  Using PreferenceFragment to effortlessly
  handle user settings
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-preferencefragment-to-effortlessly-handle-user-settings/
published: true
post_date: 2019-03-04 15:26:08
---
Handling user settings in an Android app is initially quite straightforward. Most apps use SharedPreferences to save a few booleans or strings, then read these values when necessary.

A downside of this is a lack of safety. You might save the user's name under "Username", then retrieve it as "UserName", resulting in never managing to retrieve the name! You also have to create all the switches, text fields, and sliders yourself. Luckily, PreferenceFragment provides an easy way to safely show and change user preferences.

<!--more-->

There is an <a href="https://github.com/JakeSteam/PreferencesExample" target="_blank" rel="noopener noreferrer">entire sample project available on GitHub</a>, (<a href="https://gist.github.com/JakeSteam/6937ef3b6c217a330ec4822dd8c5d1bf" target="_blank" rel="noopener noreferrer">and a Gist</a>) for those that prefer to just see the code. Note that some of the code is in Kotlin, but most is XML.
<h2>Preparing your project</h2>
Whilst we will be using PreferenceFragment in this example, we'll actually be using PreferenceFragmentCompat to ensure the app looks great on earlier API versions.

To add the preference library, add the following to your app-level <code>build.gradle</code>'s dependencies and perform a Gradle sync:
<pre>implementation 'com.android.support:preference-v7:28.0.0'</pre>
<h2>Configuring your strings</h2>
Every setting in your list needs to have a few strings defined. I recommend doing this in a separate <code>preferences.xml</code> file, to avoid them mixing with your localised strings, but this is personal preference.

For booleans and strings, you need 4 things. In this example, we're configuring a "Use mobile data" boolean switch:
<ol>
 	<li>An internal reference (the preference's name in SharedPreferences), e.g. <code>&lt;string name="pref_useMobileData"&gt;useMobileData&lt;/string&gt;</code>.</li>
 	<li>A default value, e.g. <code>&lt;bool name="pref_useMobileData_default"&gt;false&lt;/bool&gt;</code>.</li>
 	<li>A title to be displayed to the user, e.g. <code>&lt;string name="useMobileData_title"&gt;Use mobile data&lt;/string&gt;</code>.</li>
 	<li>A description to be displayed to the user, e.g. <code>&lt;string name="useMobileData_desc"&gt;Sync on mobile networks as well as wifi&lt;/string&gt;</code>.</li>
</ol>
Notice that the default value isn't a string, and is the datatype of the preference. For numerical settings, there are a few additional parameters required so that a value slider can be displayed:
<ol>
 	<li>The minimum value, e.g. <code>&lt;integer name="pref_myInt_min"&gt;1&lt;/integer&gt;</code>.</li>
 	<li>The maximum value, e.g. <code>&lt;integer name="pref_myInt_max"&gt;10&lt;/integer&gt;</code>.</li>
 	<li>The step value (how much the number can be changed by), e.g. <code>&lt;integer name="pref_myInt_step"&gt;1&lt;/integer&gt;</code>.</li>
</ol>
I've previously had problems getting step to be obeyed, but minimum and maximum work reliably. Here's a complete example of an example integer preference:
<pre>&lt;string name="pref_int1"&gt;int1&lt;/string&gt;
&lt;integer name="pref_int1_step"&gt;1&lt;/integer&gt;
&lt;integer name="pref_int1_min"&gt;0&lt;/integer&gt;
&lt;integer name="pref_int1_max"&gt;10&lt;/integer&gt;
&lt;integer name="pref_int1_default"&gt;10&lt;/integer&gt;
&lt;string name="int1_title"&gt;Set Int 1&lt;/string&gt;
&lt;string name="int1_desc"&gt;Change the value of the first integer&lt;/string&gt;</pre>
<h2>Configuring your preference UI</h2>
Now that your strings are all prepared, you can create a UI out of them. Inside the <code>/res/xml/</code> folder, create an XML file to configure your preferences screen. I've named mine <code>preferences_ui.xml</code>.

This XML file defines a <code>PreferenceScreen</code> root object, and then contains multiple <code>Preference</code>, <code>PreferenceScreen</code>, or <code>PreferenceCategory</code>s. Here's an example of each in action:
<pre>&lt;PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;PreferenceScreen
            android:title="@string/open_repo_title"
            android:icon="@drawable/ic_repo"
            android:summary="@string/open_repo_desc"&gt;
        &lt;intent android:action="android.intent.action.VIEW"
                android:data="@string/repo_url"/&gt;
    &lt;/PreferenceScreen&gt;
    &lt;PreferenceCategory android:title="Booleans"&gt;
        &lt;SwitchPreference
                android:key="@string/pref_boolean1"
                android:defaultValue="@bool/pref_boolean1_default"
                android:title="@string/boolean1_title"
                android:icon="@drawable/ic_one"
                android:summary="@string/boolean1_desc"/&gt;
        &lt;SwitchPreference
                android:key="@string/pref_boolean2"
                android:defaultValue="@bool/pref_boolean2_default"
                android:title="@string/boolean2_title"
                android:icon="@drawable/ic_two"
                android:summary="@string/boolean2_desc"/&gt;
    &lt;/PreferenceCategory&gt;
&lt;/PreferenceScreen&gt;</pre>
<h3>PreferenceScreen</h3>
Whilst a <code>PreferenceScreen</code> is the root of your UI, it can also be used to <a href="https://developer.android.com/reference/android/preference/PreferenceScreen" target="_blank" rel="noopener noreferrer">nest additional screens of preferences</a> inside your main layout. Additionally, it can be used to open a desired URL, such as an app's code repository (with the URL stored in <code>strings.xml</code>):
<pre>&lt;PreferenceScreen
        android:title="@string/open_repo_title"
        android:icon="@drawable/ic_repo"
        android:summary="@string/open_repo_desc"&gt;
    &lt;intent android:action="android.intent.action.VIEW"
            android:data="@string/repo_url"/&gt;
&lt;/PreferenceScreen&gt;</pre>
<h3>PreferenceCategory</h3>
When dealing with many options, grouping them into categories helps associate related options. To help with this, including preferences inside a <code>PreferenceCategory</code> adds a faint line between itself and other elements, and can also be given a title:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/C66346DC-657F-415A-B758-98FCE194C6CC.png.jpg"><img class="aligncenter size-medium wp-image-2415" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/C66346DC-657F-415A-B758-98FCE194C6CC.png-300x158.jpg" alt="" width="300" height="158" /></a>
<h3>Preference</h3>
Preferences are what handle the actual value changing. There's a <code>SwitchPreference</code> for booleans, <code>EditTextPreference</code> for strings, and a <code>SeekBarPreference</code> for integers among others. What they all have in common is the following attributes:
<ul>
 	<li><code>android:key</code>: The setting identifier. This should be your internal reference from earlier, e.g. <code>pref_useMobileData</code>.</li>
 	<li><code>android:defaultValue</code>: The default value. This should be your default value from earlier, e.g. <code>false</code>.</li>
 	<li><code>android:title</code>: Your title defined earlier.</li>
 	<li><code>android:summary</code>: Your description defined earlier.</li>
 	<li><code>android:icon</code>: Whilst optional, adding a drawable reference here makes each setting easily identifiable.</li>
</ul>
<h2>Setting up your fragment</h2>
Now that your <code>preferences_ui.xml</code> file is all set up, it needs to actually be utilised.

First, make a <code>PrefsFragment</code> file that extends <code>PreferenceFragmentCompat</code>. Override <code>onCreatePreferences</code> to set your custom preferences screen:
<pre>class PrefsFragment : PreferenceFragmentCompat() {
    override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
        addPreferencesFromResource(R.xml.preferences_ui)
    }
}</pre>
<h2>Navigating to your fragment</h2>
Next, add a link to your soon-to-be-created settings fragment however you currently utilise fragments. For example, I have a <code>FrameLayout</code> in my XML called <code>fragment_frame</code>, so I replace that with my fragment inside my Activity's <code>onCreate</code>:
<pre>override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    this.supportFragmentManager
        .beginTransaction()
        .replace(R.id.fragment_frame, PrefsFragment())
        .commit()
}</pre>
That's it! You can now navigate to your settings page, and save and load as many booleans, integers, and strings as you like! However, there's a lot of extra functionality that you'll be missing out on...
<h2>Adding extra functionality</h2>
<h3>Dependent boolean preferences</h3>
<code>android:dependency</code> allows a setting to be disabled until another setting has been turned on. This is useful for disabling feature configuration until the setting is enabled. To use it, just add the enabling preference's key as the <code>dependency</code> value on the preference being enabled. For example, to disable <code>boolean2</code> unless <code>boolean1</code> is turned on:
<pre>&lt;SwitchPreference
        android:key="@string/pref_boolean1"
        android:defaultValue="@bool/pref_boolean1_default"
        android:title="@string/boolean1_title"
        android:icon="@drawable/ic_one"
        android:summary="@string/boolean1_desc"/&gt;
&lt;SwitchPreference
        android:key="@string/pref_boolean2"
        android:defaultValue="@bool/pref_boolean2_default"
        android:title="@string/boolean2_title"
        android:icon="@drawable/ic_two"
        android:dependency="@string/pref_boolean1"
        android:summary="@string/boolean2_desc"/&gt;</pre>
<h3>Listening for preference changes</h3>
Another very useful addition to a preference screen is the ability to react when a setting changes. For example, my APOD Wallpaper app disables background tasks <a href="https://github.com/JakeSteam/APODWallpaper/blob/master/app/src/main/java/uk/co/jakelee/apodwallpaper/fragments/SettingsFragment.kt#L126:L132" target="_blank" rel="noopener noreferrer">as soon as background sync is turned off</a>.

This is done by letting the fragment listen to preference change events, then checking the events for the preference that is changed.

First, make your <code>PrefsFragment</code> implement <code>SharedPreferences.OnSharedPreferenceChangeListener</code>.

Next, start listening to changes inside <code>onResume</code> and stop listening inside <code>onPause</code>:
<pre>override fun onResume() {
    super.onResume()
    preferenceScreen.sharedPreferences.registerOnSharedPreferenceChangeListener(this)
}

override fun onPause() {
    super.onPause()
    preferenceScreen.sharedPreferences.unregisterOnSharedPreferenceChangeListener(this)
}</pre>
Finally, set up your change listener. The <code>key</code> passed to it is the <code>key</code> defined in your preferences XML. The preference can be retrieved with <code>findPreference(key)</code>, which can then be checked for type / value (e.g. <code>is SwitchPreference</code> or <code>pref.isChecked</code>). In this example, when the first string preference changes, display the new value in a toast:
<pre>override fun onSharedPreferenceChanged(sharedPreferences: SharedPreferences, key: String) {
    when (key) {
        getString(R.string.pref_string1) -&gt; {
            val pref = findPreference(key) as EditTextPreference
            Toast.makeText(activity!!, "String changed to ${pref.text}", Toast.LENGTH_SHORT).show()
        }
    }
}</pre>
<h3>Adding custom action in preferences</h3>
The final functionality covered is setting up a custom action. For example, you may want a button that displays the current status of the app in an <code>AlertDialog</code>. To set this up, first set up an <code>onPreferenceClickListener</code> inside <code>onCreatePreferences</code>:
<pre>override fun onCreatePreferences(savedInstanceState: Bundle?, rootKey: String?) {
    addPreferencesFromResource(R.xml.preferences_ui)
    findPreference(getString(R.string.pref_show_values)).onPreferenceClickListener = showValuesListener
}</pre>
This listener can do anything you like, and is an easy way to extend the functionality of your settings page. For example, the APOD Wallpaper app mentioned earlier <a href="https://github.com/JakeSteam/APODWallpaper/blob/master/app/src/main/java/uk/co/jakelee/apodwallpaper/fragments/SettingsFragment.kt#L96:L104" target="_blank" rel="noopener noreferrer">uses 6 listeners</a>, for everything from giving feedback to viewing current app status.
<pre>private val showValuesListener = Preference.OnPreferenceClickListener { _ -&gt;
    val prefHelper = PreferenceHelper(activity!!)
    AlertDialog.Builder(activity!!)
        .setTitle(R.string.values_title)
        .setMessage(R.string.values_text)
        .setPositiveButton(R.string.values_close) { _, _ -&gt; }
        .show()
    true
}</pre>
<h2>Conclusion</h2>
For many applications, just this preference fragment combined with change listeners will be enough to manage user preferences. Using the <code>PreferenceFragment</code> skips a lot of repetitive code involved in making a layout, and allows you to focus on correctly responding when settings change.

The next post in this series will cover programmatically getting / setting values, using the existing <code>preferences.xml</code> values.