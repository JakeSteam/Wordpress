---
ID: 2420
post_title: >
  Creating a preference utility to safely
  handle Android SharedPreferences
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-preference-utility-to-safely-handle-android-sharedpreferences/
published: true
post_date: 2019-03-05 16:30:18
---
Saving and retrieving shared preference values in Android is relatively straightforward, but doing it safely can be problematic. For example, you may set a default value, then accidentally use a different default value when retrieving!

This post will cover creating <code>PreferenceHelper</code>, a class to safely access preferences of any type. There is also a related post covering <a href="https://blog.jakelee.co.uk/using-preferencefragment-to-effortlessly-handle-user-settings/">creating a preferences screen in your app</a>.<!--more-->

This entire post is <a href="https://gist.github.com/JakeSteam/0dd41b8ffeedbc8d0d21b182f9e60357" target="_blank" rel="noopener noreferrer">available as a Gist</a>, and as an <a href="https://github.com/JakeSteam/PreferencesExample" target="_blank" rel="noopener noreferrer">example preferences project</a>.
<h2>Creating strings</h2>
For the <code>PreferenceHelper</code> class, every preference must have both a key, and a default value. These should be stored in an <code>xml</code> file so they can be referenced easily (e.g. by the UI). For example, two boolean preferences may result in the following inside <code>strings.xml</code>:
<pre>&lt;string name="pref_boolean1"&gt;boolean1&lt;/string&gt;
&lt;bool name="pref_boolean1_default"&gt;false&lt;/bool&gt;
&lt;string name="pref_boolean2"&gt;boolean2&lt;/string&gt;
&lt;bool name="pref_boolean2_default"&gt;false&lt;/bool&gt;</pre>
<h2>Creating preference utility</h2>
The <code>PreferenceHelper</code> class needs to be constructed with a context, as preferences cannot be saved / retrieved without one. This is due to the looking up of strings which takes place, as well as context being needed for the <code>SharedPreferences</code> themselves. The class also keeps a reference to the shared preferences to be used for all actions.

For each data type (just <code>boolean</code>, <code>string</code>, and <code>integer</code> in this example), 3 things are needed:
<ol>
 	<li>An enum of all preferences of this type, defining both the preference's key and default value.</li>
 	<li>A getter, which can be passed an enum to return either the saved value or the predetermined default value.</li>
 	<li>A setter, which can be passed an enum as well as a value, to then save that value.</li>
</ol>
For example, if an app has just 2 boolean options (called <code>boolean1</code> and <code>boolean2</code>), this will be the code for <code>PreferenceHelper</code>:
<pre>class PreferenceHelper(val context: Context) {
    val prefs = PreferenceManager.getDefaultSharedPreferences(context)

    enum class BooleanPref(val prefId: Int, val defaultId: Int) {
        setting1(R.string.pref_boolean1, R.bool.pref_boolean1_default),
        setting2(R.string.pref_boolean2, R.bool.pref_boolean2_default)
    }

    fun getBooleanPref(pref: BooleanPref) =
        prefs.getBoolean(context.getString(pref.prefId), context.resources.getBoolean(pref.defaultId))

    fun setBooleanPref(pref: BooleanPref, value: Boolean) =
        prefs.edit().putBoolean(context.getString(pref.prefId), value).commit()
}</pre>
This can of course be easily extended to cover strings and integers, as in <a href="https://gist.github.com/JakeSteam/0dd41b8ffeedbc8d0d21b182f9e60357#file-preferenceshelper-kt" target="_blank" rel="noopener noreferrer">this article's Gist</a>.
<h2>Using utility</h2>
All of that work ends up in a utility that is very, very simple to use, whilst never having any risk of using the wrong default value, wrong data type, etc.

A simple use case where you want to get the value of a boolean, and then change it, then repeat the process with a string, would look like:
<pre>val prefHelper = PreferenceHelper(this)

val myBoolean = prefHelper.getBooleanPref(PreferenceHelper.BooleanPref.setting1)
prefHelper.setBooleanPref(PreferenceHelper.BooleanPref.setting1, false)

val myString = prefHelper.getStringPref(PreferenceHelper.StringPref.setting1)
prefHelper.setStringPref(PreferenceHelper.StringPref.setting1, "abc")</pre>
As enums are used for the preferences, it's impossible to pass a string reference to <code>getBooleanPref</code>, and vice versa. This means preferences can be saved and accessed with complete certainty of their datatype and default value.