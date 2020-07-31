---
ID: 2775
post_title: >
  A few experiments with Android drawable
  gradients
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/a-few-experiments-with-android-drawable-gradients/
published: true
post_date: 2020-07-31 15:20:20
---
After recently struggling to make a small modification to a simple translucent overlay, I decided to experiment with gradients in Android drawables. After a few hours, I at least understand the basics now! Drawable gradients seem to be rarely used despite their simple syntax, with people preferring to use SVGs or static images.

I've included a few example outputs in this post, hopefully these help those struggling to create a gradient. I also recommend taking a look at <a href="https://angrytools.com/gradient/" target="_blank" rel="noopener noreferrer">AngryTools' gradient generator</a> (Android tab), to easily generate the XML for simpler gradients automatically.

This post is also <a href="https://github.com/JakeSteam/android-gradient-playground" target="_blank" rel="noopener noreferrer">available as a GitHub repo</a>.

<!--more-->
<table style="border-collapse: collapse; width: 100%; height: 1764px;">
<tbody>
<tr style="height: 59px;">
<th style="width: 39.574%; height: 59px;">
<h3>Notes</h3>
</th>
<th style="width: 11.0451%; height: 59px;">
<h3>Gradient</h3>
</th>
</tr>
<tr style="height: 758px;">
<td style="width: 39.574%; text-align: left; height: 758px; vertical-align: top;">
<h5>Simple linear gradient</h5>
A really simple gradient to start, our first type is "linear". All we have is a <code>startColor</code> of white and an <code>endColor</code> of black, resulting in the nice smooth transition shown.</td>
<td style="width: 11.0451%; text-align: left; height: 758px;"><img class="aligncenter" src="https://i.imgur.com/XE3sgsM.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;shape xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;gradient
        android:startColor="@android:color/white"
        android:endColor="@android:color/black"
        /&gt;
&lt;/shape&gt;</pre>
</td>
</tr>
<tr style="height: 793px;">
<td style="width: 39.574%; text-align: left; height: 793px; vertical-align: top;">
<h5>Centre offsetting</h5>
Whilst this gradient might look very similar to the previous, there's a secret inside: <code>centerY</code>.

Setting a centre colour (<code>holo_blue_dark</code> in this case) allows for slightly more complex gradients, but the interesting bit happens when you redefine where the centre is. <code>centerX</code> and <code>centerY</code> will both be used extensively throughout this post, and work by redefining where the centre colour should appear. For example, a value of <code>20%</code> or <code>0.2</code> will ensure your <code>centerColor</code> appears 20% down the drawable vertically.</td>
<td style="width: 11.0451%; text-align: left; height: 793px;"><img class="aligncenter" style="font-family: inherit; font-size: inherit;" src="https://i.imgur.com/BybAxcs.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;shape xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;gradient
        android:startColor="@android:color/white"
        android:centerColor="@android:color/holo_blue_dark"
        android:endColor="@android:color/black"
        android:centerY="0.2"
        /&gt;
&lt;/shape&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Overlapping layers</h5>
Building up on our previous gradient, adding an extra layer (via <code>layer-list</code>) drastically increases the complexity of the output. <code>layer-list</code> simply lets you overlap multiple gradients without making new <code>ImageView</code>s or drawables.

In this case, a transparent -&gt; black gradient has been added on top of our existing white -&gt; blue -&gt; black gradient, at an <code>angle</code> (essentially rotation) of 180.</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/uQRJBT2.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/white"
            android:centerColor="@android:color/holo_blue_dark"
            android:endColor="@android:color/black"
            android:centerY="0.2"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/black"
            android:endColor="@android:color/transparent"
            android:angle="180"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
&lt;/layer-list&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Overlapping layers at the same angle</h5>
Just as the last gradient overlapped gradients at right angles, you can also overlap them without rotating.

If a layer has a transparent <code>startColor</code> and <code>endColor</code>, but a non-transparent <code>centerColor</code>, it will appear as a horizontal strip of colour. Offsetting this strip using <code>centerY</code>, then repeating the process, allows a full rainbow of colours to be created.

Note that this gradient also has a base white layer, to help the gradient be visible.</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/csbnjvh.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;solid android:color="@android:color/white" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/holo_purple"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"
            android:centerY="0.4"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/transparent"
            android:centerColor="@android:color/holo_orange_light"
            android:endColor="@android:color/transparent"
            android:centerY="0.4"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/transparent"
            android:centerColor="@android:color/holo_blue_light"
            android:endColor="@android:color/transparent"
            android:centerY="0.7"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:startColor="@android:color/transparent"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/holo_green_dark"
            /&gt;
    &lt;/shape&gt;&lt;/item&gt;
&lt;/layer-list&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Radial gradients</h5>
So far we've only seen linear gradients, now we'll use radial gradients. The easiest way of thinking about them is as a "point" of colour, radiating out. <code>centerX</code> and <code>centerY</code> can be used to move this point anywhere on the canvas, in this case 10% from the left and 10% from the top.

Again, the <code>startColor</code>, <code>centerColor</code>, and <code>endColor</code> are used to set the colour, along with <code>gradientRadius</code> being used to set the overall size of the gradient.</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/FXBhk7i.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle"&gt;
    &lt;gradient
        android:type="radial"
        android:gradientRadius="100%"
        android:startColor="@android:color/black"
        android:centerColor="@android:color/holo_blue_light"
        android:centerX="0.1"
        android:centerY="0.1"
        android:endColor="@android:color/white" /&gt;
&lt;/shape&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Floating blobs</h5>
Combining multiple radial gradients using the same overlapping technique as our "vertical rainbow" before, we can end up with this collection of floating blobs.

This effect is often hunted for by people looking to create an Instagram style logo, and is very easy to achieve. So long as you know your colours, it's just a matter of adjusting the radius, x position, and y position until each blob is in the correct place.

I used this to <a href="https://stackoverflow.com/a/63162666/608312" target="_blank" rel="noopener noreferrer">answer a question on StackOverflow</a>, and the OP raised a reasonable counterpoint. If you need a particularly complex gradient, you could just try exporting an SVG / PNG. However, with vector graphics sometimes gradients aren't translated properly, and a PNG will either be too large, or may not scale perfectly to the user's screen.

If possible, creating an image as a gradient drawable will ensure it is perfect!</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/JUgEpTr.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item&gt;
        &lt;shape android:layout_height="wrap_content"&gt;
            &lt;solid android:color="@android:color/black" /&gt;
        &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="radial"
            android:gradientRadius="100%"
            android:startColor="@android:color/holo_purple"
            android:centerX="0.1"
            android:centerY="0.1"
            android:endColor="@android:color/transparent" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="radial"
            android:gradientRadius="70%"
            android:startColor="@android:color/holo_orange_light"
            android:centerX="0.8"
            android:centerY="0.5"
            android:endColor="@android:color/transparent" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="radial"
            android:gradientRadius="40%"
            android:startColor="@android:color/holo_blue_light"
            android:centerX="0.8"
            android:centerY="0.1"
            android:endColor="@android:color/transparent" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="radial"
            android:gradientRadius="70%"
            android:startColor="@android:color/holo_green_light"
            android:centerX="0.2"
            android:centerY="0.8"
            android:endColor="@android:color/transparent" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="radial"
            android:gradientRadius="50%"
            android:startColor="@android:color/holo_red_light"
            android:centerX="0.7"
            android:centerY="0.85"
            android:endColor="@android:color/transparent" /&gt;
    &lt;/shape&gt;&lt;/item&gt;

&lt;/layer-list&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Sweep gradient</h5>
The final type of gradients are sweep. These don't seem to make sense at first glance, I find the easiest way of thinking of them is as a "cone" shaped linear gradient, viewed from above. However you interpret them, they still use all the attributes we've picked up in previous gradients.</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/F0e8HPg.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle"&gt;
    &lt;gradient
        android:startColor="@android:color/holo_blue_dark"
        android:endColor="@android:color/holo_orange_dark"
        android:type="sweep" /&gt;
&lt;/shape&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Sweep offsets</h5>
Whilst not quite as pretty as the "rainbow" or "floating blobs" seen with other gradients, offsetting radial drawables creates a pretty odd effect.

I'm not <strong>entirely</strong> sure I can think of a valid use case for this, but presumably someone needs a gradient that looks like an open doorway!

&nbsp;

&nbsp;</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/MLg3Ecx.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;solid android:color="@android:color/black" /&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.3"
            android:centerY="0.1"
            android:startColor="@android:color/holo_red_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.8"
            android:centerY="0.3"
            android:startColor="@android:color/holo_blue_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.1"
            android:centerY="0.5"
            android:startColor="@android:color/holo_green_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.6"
            android:centerY="0.7"
            android:startColor="@android:color/holo_orange_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/item&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.1"
            android:centerY="0.9"
            android:startColor="@android:color/holo_purple"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/item&gt;

&lt;/layer-list&gt;</pre>
</td>
</tr>
<tr style="height: 22px;">
<td style="width: 39.574%; text-align: left; height: 22px; vertical-align: top;">
<h5>Sweep rotations</h5>
Just like the last gradient, I can't think of a valid use case for this! Wrapping each <code>shape</code> inside a <code>rotate</code> allows rotating it at any angle, although interestingly it still retains the "edges" from the original shape. I suspect this is solvable by rotating the gradient not the shape, which might produce a more aesthetically pleasing output.</td>
<td style="width: 11.0451%; text-align: left; height: 22px;"><img class="aligncenter" src="https://i.imgur.com/ilZTCMJ.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item&gt;&lt;shape&gt;
        &lt;solid android:color="@android:color/black" /&gt;
    &lt;/shape&gt;&lt;/item&gt;

    &lt;item&gt;&lt;rotate android:fromDegrees="66" android:toDegrees="0" android:pivotY="50%" android:pivotX="50%"&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.3"
            android:centerY="0.5"
            android:startColor="@android:color/holo_red_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/rotate&gt;&lt;/item&gt;

    &lt;item&gt;&lt;rotate android:fromDegrees="290" android:toDegrees="0" android:pivotY="40%" android:pivotX="30%"&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.8"
            android:centerY="0.3"
            android:startColor="@android:color/holo_blue_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/rotate&gt;&lt;/item&gt;

    &lt;item&gt;&lt;rotate android:fromDegrees="18" android:toDegrees="0" android:pivotY="40%" android:pivotX="30%"&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.1"
            android:centerY="0.5"
            android:startColor="@android:color/holo_green_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/rotate&gt;&lt;/item&gt;

    &lt;item&gt;&lt;rotate android:fromDegrees="280" android:toDegrees="0" android:pivotY="50%" android:pivotX="30%"&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.6"
            android:centerY="0.7"
            android:startColor="@android:color/holo_orange_dark"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/rotate&gt;&lt;/item&gt;

    &lt;item&gt;&lt;rotate android:fromDegrees="0" android:toDegrees="0" android:pivotY="50%" android:pivotX="30%"&gt;&lt;shape&gt;
        &lt;gradient
            android:type="sweep"
            android:centerX="0.0"
            android:centerY="0.0"
            android:startColor="@android:color/holo_purple"
            android:centerColor="@android:color/transparent"
            android:endColor="@android:color/transparent"/&gt;
    &lt;/shape&gt;&lt;/rotate&gt;&lt;/item&gt;

&lt;/layer-list&gt;</pre>
</td>
</tr>
<tr>
<td style="width: 39.574%; text-align: left; vertical-align: top;">
<h5>Sweep split</h5>
Finally, one little curiosity about sweep gradients. By offsetting the <code>centerX</code> very far to the left, you'll end up with a top and bottom that are solid colours. This creates an interesting effect, although it could obviously be done much easier by using <code>solid</code>!

If you need a similar gradient, I recommend reading the <a href="https://stackoverflow.com/questions/4381033/multi-gradient-shapes" target="_blank" rel="noopener noreferrer">answers on this StackOverflow question</a> for simpler ways of achieving it.</td>
<td style="width: 11.0451%; text-align: left;"><img class="aligncenter" src="https://i.imgur.com/QFmIrqX.png" />
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="rectangle"&gt;
    &lt;gradient
        android:startColor="@android:color/red"
        android:endColor="@android:color/holo_orange_dark"
        android:centerX="-99"
        android:type="sweep" /&gt;
    &lt;corners android:radius="20dp" /&gt;
&lt;/shape&gt;</pre>
</td>
</tr>
</tbody>
</table>