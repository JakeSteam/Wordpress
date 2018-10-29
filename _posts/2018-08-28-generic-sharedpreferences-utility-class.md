---
ID: 1639
post_title: Generic SharedPreferences Utility Class
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/generic-sharedpreferences-utility-class/
published: true
post_date: 2018-08-28 20:42:59
---
Recently, a project required both backed up and non-backed up SharedPreferences, as well as an easy way to read and write these values. The following class was created with this functionality, using generics in Kotlin for practice. This post will walkthrough some of the key features, the finished code is also <a href="https://gist.github.com/JakeSteam/2e32518fd026adf5252d8ff18787c97e" target="_blank" rel="noopener">available as a Gist</a>.

<!--more-->
<h3>Generic get &amp; set</h3>
Most importantly, the datatype to read / write is determined by the default value passed. E.g. if the default datatype is a string, <code>getString</code> will be used. This can be a problem when storing numbers, as passing a long for a value saved as an int will not return the correct SharedPreference.

The result of the SharedPreference setting is returned as a boolean so the caller can have confidence in the save's success.
<pre>
    fun  set(key: String, value: T, toBackedUp: Boolean = true): Boolean {
        val preferences = if (toBackedUp) backedUpPreferences else nonBackedUpPreferences
        if (preferences != null &amp;&amp; !TextUtils.isEmpty(key)) {
            val editor = preferences.edit()
            when (value) {
                is String -&gt; editor.putString(key, value)
                is Long -&gt; editor.putLong(key, value)
                is Int -&gt; editor.putInt(key, value)
                is Float -&gt; editor.putFloat(key, value)
                is Boolean -&gt; editor.putBoolean(key, value)
                else -&gt; return false
            }
            return editor.commit()
        }
        return false
    }</pre>
<h3>Backed up and non-backed up preferences</h3>
As some preferences should be backed up (e.g. user choices), and others not (e.g. current app state), there are 2 SharedPreferences files used. By default, all values are read from / written to the backed up file, but this can be overridden by passing false to the get / set function.
<pre>

    private val backedUpPreferences: SharedPreferences?
    private val nonBackedUpPreferences: SharedPreferences?

    init {
        backedUpPreferences = application.getSharedPreferences(BACKED_UP_NAME, MODE_PRIVATE)
        nonBackedUpPreferences = application.getSharedPreferences(NON_BACKED_UP_NAME, MODE_PRIVATE)
    }

    companion object {
        private const val BACKED_UP_NAME = "com.yourcompany.BACKED_UP"
        private const val NON_BACKED_UP_NAME = "com.yourcompany.NON_BACKED_UP"
    }</pre>
The backup logic is added via a file called <code>backup_rules.xml</code>, in <code>/src/main/res/xml</code> containing the following:
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;full-backup-content&gt;
    &lt;include domain="sharedpref" path="com.yourcompany.BACKED_UP.xml" /&gt;
&lt;/full-backup-content&gt;</pre>
The following should also be added to the <code>AndroidManifest.xml</code> inside the <code>application</code> element:
<pre>
        android:allowBackup="true"
        android:fullBackupContent="@xml/backup_rules"</pre>
<h3>Legacy support</h3>
To ensure that legacy (non-generic) SharedPreferences uses can be easily replaced, get &amp; set wrappers are provided for the 4 major types (string, long, integer, boolean).
<pre>

    fun getStringPreference(key: String, defaultValue: String = ""): String = get(key, defaultValue)
    fun getLongPreference(key: String, defaultValue: Long = 0L): Long = get(key, defaultValue)
    fun getIntegerPreference(key: String, defaultValue: Int = 0): Int = get(key, defaultValue)
    fun getBooleanPreference(key: String, defaultValue: Boolean = false): Boolean = get(key, defaultValue)

    fun setStringPreference(key: String, value: String): Boolean = set(key, value)
    fun setLongPreference(key: String, value: Long): Boolean = set(key, value)
    fun setIntegerPreference(key: String, value: Int): Boolean = set(key, value)
    fun setBooleanPreference(key: String, value: Boolean): Boolean = set(key, value)</pre>
<h3>Possible improvements</h3>
Currently, any object can be passed to the getters or setters, not just the 4 supported types. A future improvement would be to only accept values actually supported for storage.

Additionally, a way to enforce certain keys always getting / setting the same type would help prevent hard to track down errors, as would checking both SharedPreferences files for a given key.

All code is <a href="https://gist.github.com/JakeSteam/2e32518fd026adf5252d8ff18787c97e" target="_blank" rel="noopener">available in a Gist</a>.