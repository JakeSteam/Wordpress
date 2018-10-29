---
ID: 1689
post_title: >
  Recolouring / modifying multi-layer
  drawables dynamically in Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/recolouring-modifying-multi-layer-drawables-dynamically-in-android/
published: true
post_date: 2018-09-14 10:00:32
---
Often when creating interfaces in Android, it can be more efficient to have a single <code>.xml</code> drawable and recolouring it according to requirements, instead of trying to include all possible colours in advance. Similarly, it can be more efficient to replace the drawable used inside another drawable dynamically. However, if this needs to be done multiple times within one drawable it becomes a bit more complex, as any modifications will affect the entire drawable.

This post is also <a href="https://gist.github.com/JakeSteam/1113e35ab13d4998f94d2d4a14c720f8" target="_blank" rel="noopener">available as a Gist</a>.

<!--more-->
<h3>Creating the drawable XML</h3>
For this example, a simple drawable with a coloured circle background (ID <code>background_circle</code>) and a vector image (ID <code>foreground_icon</code>) will be used. The following <code>.xml</code> should be placed at <code>/res/drawable/dynamic_drawable.xml</code>:
<pre>
&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item
        android:id="@+id/background_circle"&gt;
        &lt;shape android:shape="oval"&gt;
            &lt;solid android:color="@color/orange" /&gt;
        &lt;/shape&gt;
    &lt;/item&gt;
    &lt;item android:id="@+id/foreground_icon"&gt;
        &lt;shape&gt;
            &lt;solid android:color="@android:color/transparent" /&gt;
        &lt;/shape&gt;
    &lt;/item&gt;
&lt;/layer-list&gt;</pre>
<h3>Recolouring part of the drawable</h3>
The first half of the changes to be made are changing the background colour of our drawable.

First, get a reference to your target ImageView (either in a layout or inflated dynamically), and retrieve its drawable as a LayerDrawable (as we need to handle layers):
<pre>
val layerDrawable = findViewById(R.id.my_drawable).drawable as LayerDrawable</pre>
Next, get the background layer specifically using the <code>background_circle</code> ID from earlier:
<pre>
val backgroundDrawable = layerDrawable.findDrawableByLayerId(R.id.background_circle) as GradientDrawable</pre>
The colour can now be set using the normal <code>setColor()</code> method:
<pre>
backgroundDrawable.setColor(ContextCompat.getColor(context, R.color.my_colour))</pre>
<h3>Changing part of the drawable</h3>
The second half of the changes is changing the foreground drawable.

Using the <code>layerDrawable</code> from earlier, set the <code>foreground_icon</code> to an existing drawable resource:
<pre>
layerDrawable.setDrawableByLayerId(R.id.foreground_icon, ContextCompat.getDrawable(context, R.drawable.new_foreground_icon))</pre>
That's it! Both the composite drawable and background color have been changed, and this technique can be used for as many layers as required.
<h3>Conclusion</h3>
The ability to create complex dynamic drawables using layer-lists is extremely beneficial when attempting to minimise app size, as it means large areas of an app can use the same base layout, and just customise the small parts that are different.

For example, in the real world example this post is based on, a total of 5 foreground icons and 7 background colours were possible, meaning a total of 35 different icons were required. Using drawable layer modification, this could all be done within a few lines of code, instead of tens of resources! Much more advanced layer modifications are also possible, to create more complex differences.

All code used in this post is <a href="https://gist.github.com/JakeSteam/1113e35ab13d4998f94d2d4a14c720f8" target="_blank" rel="noopener">available as a Gist</a>.