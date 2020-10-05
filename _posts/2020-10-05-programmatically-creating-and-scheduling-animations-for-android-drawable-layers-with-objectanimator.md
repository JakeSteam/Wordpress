---
ID: 2896
post_title: >
  Programmatically creating and scheduling
  animations for Android drawable layers
  with ObjectAnimator
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/programmatically-creating-and-scheduling-animations-for-android-drawable-layers-with-objectanimator/
published: true
post_date: 2020-10-05 17:00:06
---
Recently I needed to perform a pretty complex animation on an image button. The button needed to move, whilst different parts of it also needed to rotate, fade in and out, and disappear!

To implement this I combined the animating parts into a single layer list, where they could then be animated without requiring new views. The end result was very smooth, with a very small amount of code written.

In this post we'll perform similar effects on a layer list drawable without any XML animations:

[video width="1280" height="768" webm="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/ezgif.com-gif-maker.webm"][/video]

This post is available as a <a href="https://github.com/JakeSteam/animating-layerlists" target="_blank" rel="noopener noreferrer">proof of concept repository</a>, and as <a href="https://gist.github.com/JakeSteam/c610598867b80fde6fedfc74ce282dd0" target="_blank" rel="noopener noreferrer">a GitHub gist</a>.
<!--more-->
<h2>Preparing the drawable</h2>
The drawable needs to be a <code>layer-list</code>, with each animation layer having a unique ID. Any sort of <code>item</code> should work, this example will use a shape and a vector drawable.

Since the icon needs to rotate, it needs to be inside a <code>&lt;rotate&gt;</code> tag:
<pre>&lt;layer-list xmlns:android="http://schemas.android.com/apk/res/android"&gt;
    &lt;item android:id="@+id/red_circle"&gt;
        &lt;shape android:shape="oval" &gt;
            &lt;solid android:color="@android:color/holo_red_dark" /&gt;
        &lt;/shape&gt;
    &lt;/item&gt;
    &lt;item android:id="@+id/icon"&gt;
        &lt;rotate android:drawable="@drawable/ic_launcher_foreground" /&gt;
    &lt;/item&gt;
&lt;/layer-list&gt;</pre>
<h2>Selecting layers to animate</h2>
To select a layer of the <code>ImageView</code> for animation, we first extract the <code>LayerDrawable</code>, then find the layer we want by ID:
<pre>val target = findViewById&lt;ImageView&gt;(R.id.imageview)
val animationLayers = target.drawable as LayerDrawable    
val redCircle = animationLayers.findDrawableByLayerId(R.id.red_circle) as GradientDrawable</pre>
The type of <code>Drawable</code> required varies depending on how the layer-list is defined. For this example, we only need <code>GradientDrawable</code> for our simple shape, and <code>RotateDrawable</code> for the icon we want to rotate.

There are 40 subclasses of <code>Drawable</code>, but these 2 will do for now!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/eVGhPpe.png"><img class="aligncenter size-full wp-image-2898" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/eVGhPpe.png" alt="" width="1148" height="336" /></a>

Looking at these <code>.class</code> files will tell you what animations are supported. For example, <code>RotateDrawable</code> has <code>onLevelChange</code> for rotation, but <code>GradientDrawable</code> has <code>setAlpha</code> for changing transparency.
<h2>Preparing animations</h2>
All of our animation is going to happen inside a <code>startAnimation(target: ImageView)</code> function, and use <code>ObjectAnimator</code>. Since we're going to be animating both <code>ImageView</code>s and layer list layers, an important note is needed:
<ul>
 	<li>If an <code>ImageView</code> is being used, a <strong>float</strong> value of the target will be used. For example, the alpha is between 1.0f (fully visible) and 0.0f (fully invisible).</li>
 	<li>If a <code>Drawable</code> is being used (e.g. <code>GradientDrawable</code> / <code>RotateDrawable</code>), an <strong>int</strong> value of the target will be used. For example, the alpha is instead between 255 (fully visible) and 0 (fully invisible).</li>
</ul>
<code>ObjectAnimator</code> provides a simple syntax for creating animations. If we want the red circle layer to fade from fully visible to invisible in 1000ms, we just need:
<pre>val redCircleFadeOut = ObjectAnimator.ofInt(redCircle, "alpha", 255, 0).setDuration(1000)</pre>
Simple!

We need 6 animations in total to move our <code>ImageView</code> left, partially fade out our red circle, rotate our icon left, and then reverse all 3. Note that rotation uses 0-10000, whilst alpha uses 0-255:
<pre>val moveImageViewLeft = ObjectAnimator.ofFloat(target, View.TRANSLATION_X, -200f).setDuration(6000)
val moveImageViewRight = ObjectAnimator.ofFloat(target, View.TRANSLATION_X, 0f).setDuration(6000)
val redCircleFadeOut = ObjectAnimator.ofInt(redCircle, "alpha", 255, 100).setDuration(1000)
val redCircleFadeIn = ObjectAnimator.ofInt(redCircle, "alpha", 100, 255).setDuration(100)
val iconRotateLeft = ObjectAnimator.ofInt(icon, "level", 10000, 1000).setDuration(3000)
val iconRotateRight = ObjectAnimator.ofInt(icon, "level", 1000, 10000).setDuration(300)</pre>
<h2>Scheduling animations</h2>
Using <code>AnimatorSet</code>'s very simple <code>play(x).with(y).after(z)</code> syntax, complex scheduling becomes very readable.

In our animation, we want to:
<ol>
 	<li>Move our <code>ImageView</code> left, fade out our red circle, rotate our icon left.</li>
 	<li>After the icon has finished rotating, rotate it back again and fade the circle back in.</li>
 	<li>After the <code>ImageView</code> has finished moving, move it back.</li>
</ol>
Using <code>AnimatorSet</code> this can be written as:
<pre>AnimatorSet().apply {
  play(moveImageViewLeft)
    .with(redCircleFadeOut)
    .with(iconRotateLeft)
  play(redCircleFadeIn)
    .with(iconRotateRight)
    .after(iconRotateLeft)
  play(moveImageViewRight)
    .after(moveImageViewLeft)
  start()
}</pre>
<h2>Handling multiple animations</h2>
In the real world, when performing an animation it may be triggered again. When this situation is not handled, all sorts of odd visual effects happen and look awful!
<h3>Avoiding overlapping animations</h3>
A simple way to avoid overlapping animations is keeping track of the <code>AnimatorSet</code> and cancelling it when a new animation is started:
<pre>private var animatorSet: AnimatorSet? = null
private fun startAnimation(target: ImageView) {
  animatorSet?.cancel()
  animatorSet = AnimatorSet().apply {
    ...
    start()
  }
}</pre>
<h3>Resetting view positions</h3>
Whilst most of the layer effects automatically reset when cancelled, the view's movement is not. As such, we need to create a "reset" function:
<pre>val animationReset = { target.translationX = 0f }</pre>
This should be triggered when the animation is cancelled (due to a new animation starting), so <code>doOnCancel()</code> should be used. If your animation does not leave views in the same place as they started, <code>doOnEnd()</code> should also be used:
<pre>AnimatorSet().apply {
    ...
    doOnCancel { animationReset.invoke() }
    start()
  }
}
</pre>
<h2>Resources</h2>
<ul>
 	<li>A <a href="https://github.com/JakeSteam/animating-layerlists" target="_blank" rel="noopener noreferrer">proof of concept repository</a> is available</li>
 	<li>A <a href="https://gist.github.com/JakeSteam/c610598867b80fde6fedfc74ce282dd0" target="_blank" rel="noopener noreferrer">GitHub gist</a> is available</li>
 	<li><a href="https://developer.android.com/reference/android/animation/ObjectAnimator" target="_blank" rel="noopener noreferrer">ObjectAnimator on developer docs</a></li>
 	<li><a href="https://developer.android.com/reference/android/view/animation/AnimationSet" target="_blank" rel="noopener noreferrer">AnimationSet on developer docs</a></li>
</ul>