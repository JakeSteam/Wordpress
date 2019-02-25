---
ID: 2391
post_title: >
  How to add padding to an Android vector
  drawable
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-add-padding-to-an-android-vector-drawable/
published: true
post_date: 2019-02-25 16:30:03
---
When adding icons to Android apps, you'll generally be working with square icons. For example, the excellent built-in vector icon library (<a href="https://material.io/tools/icons/?style=baseline" target="_blank" rel="noopener noreferrer">also available online</a>) only contains perfectly square icons.

Sometimes however, these vector icons are used in a non-square place. With a normal image file adding extra padding is easy, not so with a vector file!

This tutorial will cover how to modify a square vector image to add any padding you want, without any changes to your app's layouts. The icon used is a white <code><a href="https://material.io/tools/icons/?search=tim&amp;icon=access_time">access_time</a></code>, used to represent a loading state.

<!--more-->

In my app, a <code>StaggeredGridLayoutManager</code> is used to display 3 movie posters per row. These movie posters are always the same size, and a ratio of 1:1.5 width:height.
<h2>Using the icon</h2>
If the <code>ImageView</code>s are initially set to our loading icon (either as a default image or a Picasso placeholder), the size difference is immediately obvious:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/1.png"><img class="aligncenter size-full wp-image-2394" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/1.png" alt="" width="448" height="134" /></a>
<pre>&lt;vector android:height="24dp"
    android:viewportHeight="24.0" android:viewportWidth="24.0"
    android:width="24dp" xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;path android:fillColor="#FFFFFF" android:pathData="M11.99,2C6.47,2 2,6.48 2,12s4.47,10 9.99,10C17.52,22 22,17.52 22,12S17.52,2 11.99,2zM12,20c-4.42,0 -8,-3.58 -8,-8s3.58,-8 8,-8 8,3.58 8,8 -3.58,8 -8,8zM12.5,7H11v6l5.25,3.15 0.75,-1.23 -4.5,-2.67z"/&gt;
&lt;/vector&gt;</pre>
<h2>Resizing our vector drawable</h2>
Our vector image is too small! This is due to the <code>android:height="24dp" android:width="24dp"</code> initially included in the icon, enforcing a maximum size. Setting these both to <code>240dp</code> fixes the horizontal spacing issue, but the 1:1 ratio conflicts with the movie poster's 1:1.5. This results in the movie posters constantly pushing each other and the loading icons around as they load, making for a very unappealing UI.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/2.png"><img class="aligncenter size-full wp-image-2395" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/2.png" alt="" width="451" height="253" /></a>
<pre>&lt;vector android:height="240dp"
    android:viewportHeight="24.0" android:viewportWidth="24.0"
    android:width="240dp" xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;path android:fillColor="#FFFFFF" android:pathData="M11.99,2C6.47,2 2,6.48 2,12s4.47,10 9.99,10C17.52,22 22,17.52 22,12S17.52,2 11.99,2zM12,20c-4.42,0 -8,-3.58 -8,-8s3.58,-8 8,-8 8,3.58 8,8 -3.58,8 -8,8zM12.5,7H11v6l5.25,3.15 0.75,-1.23 -4.5,-2.67z"/&gt;
&lt;/vector&gt;</pre>
<h2>Adjusting the ratio of our vector drawable</h2>
So, the drawable needs to be resized so it is the same ratio as the eventual real content. For example, a width of 200 would require a height of 300. Making these changes stops the movie posters / loading icons pushing each other around, but the icon has become stretched!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/3.png"><img class="aligncenter size-full wp-image-2396" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/3.png" alt="" width="452" height="352" /></a>
<pre>&lt;vector android:height="300dp"
    android:viewportHeight="24.0" android:viewportWidth="24.0"
    android:width="200dp" xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;path android:fillColor="#FFFFFF" android:pathData="M11.99,2C6.47,2 2,6.48 2,12s4.47,10 9.99,10C17.52,22 22,17.52 22,12S17.52,2 11.99,2zM12,20c-4.42,0 -8,-3.58 -8,-8s3.58,-8 8,-8 8,3.58 8,8 -3.58,8 -8,8zM12.5,7H11v6l5.25,3.15 0.75,-1.23 -4.5,-2.67z"/&gt;
&lt;/vector&gt;</pre>
<h2>Adding padding inside our resource</h2>
The solution is to add padding to our vector drawable, to keep the original 1:1 ratio. This is done by adding a <code>&lt;group&gt;</code> around the <code>&lt;path&gt;</code>. This group will scale the content inside it (the actual icon), without affecting the overall size / ratio of the vector. Since the <code>scaleX</code> and <code>scaleY</code> parameters are relative, they must be set to 1 or less to avoid cropping. For example, setting <code>scaleX</code> to <code>0.99</code> and <code>scaleY</code> to <code>0.66</code> will obey the 1:1.5 ratio required. Note that <code>pivotX</code> and <code>pivotY</code> set the centre point of the scaling, and should be half of your <code>viewpointWidth</code> and <code>viewpointHeight</code> (e.g. 12).

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/4.png"><img class="aligncenter size-full wp-image-2397" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/4.png" alt="" width="452" height="433" /></a>
<pre>&lt;vector android:height="300dp"
    android:viewportHeight="24.0" android:viewportWidth="24.0"
    android:width="200dp" xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;group
        android:scaleX="0.99"
        android:scaleY="0.66"
        android:pivotX="12"
        android:pivotY="12"&gt;
        &lt;path android:fillColor="#FFFFFF" android:pathData="M11.99,2C6.47,2 2,6.48 2,12s4.47,10 9.99,10C17.52,22 22,17.52 22,12S17.52,2 11.99,2zM12,20c-4.42,0 -8,-3.58 -8,-8s3.58,-8 8,-8 8,3.58 8,8 -3.58,8 -8,8zM12.5,7H11v6l5.25,3.15 0.75,-1.23 -4.5,-2.67z"/&gt;
    &lt;/group&gt;
&lt;/vector&gt;</pre>
Finally, it looks reasonable! Note that if we wanted more padding on the sides, a <code>scaleX</code> of <code>0.8</code> and <code>scaleY</code> of <code>0.4</code> would increase the padding on all sides.

Hopefully this tutorial has showed how <code>group</code> can be to scale the content of an SVG down. This technique can even be applied to individual parts of an SVG if there are multiple paths!

&nbsp;