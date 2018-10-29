---
ID: 1629
post_title: Creating a custom Android ActionBar
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-custom-android-actionbar/
published: true
post_date: 2018-08-22 22:03:53
---
By default, new Android projects have an ActionBar at the top (also known as a title bar), which usually contains a title, an optional back button on the left, and optional action(s) on the right. For many cases, minor customisations to colour are enough, but if a project requires exactly meeting a client's design more advanced functionality will need to be utilised.

This tutorial will walk through the steps needed to turn a default ActionBar into a fully customised area of the screen, whilst keeping useful functionality like displaying a back button intact. Kotlin is used for <a href="https://github.com/JakeSteam/BlogCustomActionBar" target="_blank" rel="noopener">this sample project</a>, but all code can be easily converted to Java.

<!--more-->
<h3>Retrieving the ActionBar</h3>
Retrieving the ActionBar is a simple case of <code>myBar = actionBar!!</code> in an Activity, or <code>myBar = supportActionBar!!</code> in an AppCompatActivity. The assignment is forced (<code>!!</code>) since we know the action bar will exist.
<h3>Removing default effects</h3>
First, the elevation needs to be removed using <code>myBar.elevation = 0f</code>. By default, the elevation causes shadow to be cast on the rest of your screen, and also forces a potentially unwanted dividing line between the ActionBar. Once this is removed, the action bar can blend seamlessly into the content of the app (once we set up a custom view later on).
<img class="alignnone size-full wp-image-1631" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/1.png" alt="1" width="1080" height="236" />
<img class="alignnone size-full wp-image-1632" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/2.png" alt="2" width="1080" height="236" />
<h3>Creating a custom layout</h3>
Now our custom layout needs to be created. The design of this of course depends on your requirements, but the overall structure should be a <code>RelativeLayout</code> at <code>res/layout/element_toolbar.xml</code> with the following layout parameters:
<pre>
&lt;RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:layout_width="match_parent"
android:layout_height="wrap_content"
android:layout_gravity="fill_horizontal"&gt;</pre>
This layout functions just like any other layout file, and can be filled with ImageViews, TextViews, etc.
<h3>Setting a custom layout</h3>
Next, our custom layout needs to be enabled and configured:
<pre>
myBar.setDisplayShowCustomEnabled(true)
myBar.setCustomView(R.layout.element_toolbar)</pre>
Additionally, since we're going to be handling title text in our <code>element_toolbar.xml</code>, we should turn off displaying text:
<pre>
myBar.setDisplayShowTitleEnabled(false)</pre>
<img class="alignnone size-full wp-image-1633" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/3.png" alt="3" width="1080" height="236" />

We now have a custom toolbar! However, there's a pretty glaring issue, in that our custom layout doesn't actually reach the edges of the ActionBar, so the default design can still be seen. To fix that we need to...
<h3>Override ActionBar background</h3>
First, inside <code>AppTheme</code> in <code>/res/values/styles.xml</code> add the following line:
<pre>
&lt;item name="actionBarStyle"&gt;@style/CustomBar&lt;/item&gt;</pre>
And add the following style inside the same file, but outside of <code>AppTheme</code>:
<pre>
&lt;style name="CustomBar" parent="Widget.AppCompat.ActionBar.Solid"&gt;
    &lt;item name="background"&gt;@android:color/black&lt;/item&gt;
&lt;/style&gt;</pre>
<img class="alignnone size-full wp-image-1634" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/4.png" alt="4" width="1080" height="236" />
<h3>Next steps</h3>
An example project is provided for this tutorial, with an example of how to set click events for items inside your custom ActionBar. <a href="https://github.com/JakeSteam/BlogCustomActionBar" target="_blank" rel="noopener">It can be downloaded / forked from GitHub</a>.

If you have any questions about the topic covered, please comment or join the <a href="https://discord.gg/xAkTmkZ" target="_blank" rel="noopener">Android Dev discord</a>.