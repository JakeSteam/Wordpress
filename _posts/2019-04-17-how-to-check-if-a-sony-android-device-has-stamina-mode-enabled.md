---
ID: 2466
post_title: 'How to check if a Sony Android device has &#8220;Stamina mode&#8221; enabled'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-check-if-a-sony-android-device-has-stamina-mode-enabled/
published: true
post_date: 2019-04-17 20:59:55
---
Whilst detecting power saving mode (at least on Lollipop+) is <a href="https://blog.jakelee.co.uk/displaying-a-power-saving-enabled-bar-inside-your-android-app/">very easy</a>, some manufacturers implement their own battery saving systems. One of the least standards-conforming of these is <a href="https://support.sonymobile.com/gb/xperiam2/faq/battery,-power-&amp;-charging/023101886c027a68013a30335ef3007772/" target="_blank" rel="noopener noreferrer">Sony's Stamina Mode</a>. Luckily this is easily detected, albeit in an unofficial way.

<!--more-->

It's important to note that this method only detects if the mode is <em>enabled in general</em>, not whether it is currently in use. For example, if a user has the mode set to activate on &lt;15% battery, this method will return true even if the battery is currently 100%. Regardless, since the only official response is <a href="https://stackoverflow.com/a/19823306/608312" target="_blank" rel="noopener noreferrer">"there is no official way" from 5 years ago</a>, this is the best available for now!

First, check the device is a Sony device. If it's not, stamina mode definitely won't be on!
<pre><span class="pl-k">if</span> (<span class="pl-en">Build</span>.<span class="pl-en">MANUFACTURER</span>.toUpperCase() <span class="pl-k">==</span> <span class="pl-s"><span class="pl-pds">"</span>SONY<span class="pl-pds">"</span></span>) {</pre>
Next, check if the <code>somc.stamina_mode</code> settings value is set to 1. This is done by using <code>android.provider.Settings</code>, you may need to replace <code>contentResolver</code> with <code>context.getContentResolver()</code>:
<pre><span class="pl-en">Settings</span>.<span class="pl-en">Secure</span>.getInt(contentResolver, <span class="pl-s"><span class="pl-pds">"</span>somc.stamina_mode<span class="pl-pds">"</span></span>, <span class="pl-c1">0</span>) <span class="pl-k">==</span> <span class="pl-c1">1</span></pre>
For example, a Kotlin function to display a bar if Sony's Stamina Mode is on may look like:
<pre>private fun performSonyPowerSavingCheck() {
    if (Build.MANUFACTURER.toUpperCase() == "SONY") {
        togglePowerSavingBar(
            Settings.Secure.getInt(contentResolver, "somc.stamina_mode", 0) == 1
        )
    }
}</pre>