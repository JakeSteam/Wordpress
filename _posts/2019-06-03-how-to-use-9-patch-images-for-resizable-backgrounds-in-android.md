---
ID: 2504
post_title: >
  How to use 9-patch images for resizable
  backgrounds in Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-use-9-patch-images-for-resizable-backgrounds-in-android/
published: true
post_date: 2019-06-03 16:24:19
---
In Android, almost all views can have a background colour or image set. Whilst a colour can be any size / shape, as can a vector drawable, a bitmap drawable cannot. For example, trying to make a 100px wide &amp; 100px tall image 500px wide and 50px tall would result in a blurry, and horribly distorted background.

My early apps suffered from this often (e.g. <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener noreferrer">Pixel Blacksmith</a>), but there is a solution! So long as the image you're using has consistent (stretchable) bars on the top and side, 9 patch images may be the answer.<!--more-->

<a href="https://developer.android.com/studio/write/draw9patch" target="_blank" rel="noopener noreferrer">9 patch images</a> are a type of PNG (identified by their <code>.9.png</code> file extension) that define which parts of the image can be "stretched" to fill the required space. In this example, the corners of a background image will be marked non-stretchable, whilst the top and bottom sections will be marked as stretchable. This will result in the image being able to fit any <code>ImageView</code> or background.
<h2>Normal PNG</h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/zzzKxNu.png"><img class="size-medium wp-image-2506 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/zzzKxNu-181x300.png" alt="" width="181" height="300" /></a>Just setting the PNG as the background technically works, as the image resizes, but the flaws are immediately obvious if you click the thumbnail on the left to view the full image.

The corners are impressively both pixellated AND blurry, as Android tries to scale the small image to a much larger area. This effect is most obvious on "Wide", but even "Small" shows some hints of it.

&nbsp;
<h6></h6>
<h2>9-Patch PNG</h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Annotation-2019-06-03-161729.jpg"><img class="alignleft size-medium wp-image-2514" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Annotation-2019-06-03-161729-169x300.jpg" alt="" width="169" height="300" /></a>Once the image has been converted to a 9-patch, it handles all of the earlier situations perfectly. There is no blurriness or pixellation, and the "Small" square is handled just as gracefully as the larger ones.

This tutorial will walk you through the 9-patch editor, and provide a few tips for converting your own PNGs.
<h6></h6>
<h2>Introduction to 9-patch PNG editor</h2>
Android Studio's built in 9-patch editor can be intimidating at first glance, but it's actually very, very simple. All it does it allow you to set stretchable areas by modifying the black / transparent pixels around the edge of your image. It will also help warn you of potential issues with your 9-patch.

To create your first 9-patch:
<ol>
 	<li>Inside Android Studio, right click your PNG inside <code>res/drawable</code>.</li>
 	<li>Select "Create 9-Patch file...".</li>
 	<li>Select where the file should be placed (usually the same <code>res/drawable</code> folder).</li>
 	<li>This will open up the 9 patch editor, described below:</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/X8CYLz1.png"><img class="aligncenter size-full wp-image-2508" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/X8CYLz1.png" alt="" width="784" height="402" /></a>

The 9-patch editor consists of:
<ol>
 	<li>The 9-patch's name.</li>
 	<li>The editing window, where the scalable areas can be defined.</li>
 	<li>A transparent bordered area, meaning this area <strong>cannot</strong> be stretched.</li>
 	<li>A black bordered area, meaning this area <strong>can</strong> be stretched.</li>
 	<li>When hovering over a side of the image, a "resize" icon lets you modify the stretchable / non-stretchable area.</li>
 	<li>The preview pane, where the 9-patch's appearance can be seen.</li>
 	<li>Adjusting zoom in the editing window.</li>
 	<li>Adjusting zoom in the preview window.</li>
 	<li>Additional options, consisting of:
<ol>
 	<li>Show lock: This highlights (in the preview pane, when moused over) which parts of the image can be changed for the 9 patch (a border of 1px).</li>
 	<li>Show content: This highlights (in the preview pane) which parts of the drawable can contain content if it is used as a background. For example, on a <code>TextView</code> it will highlight where text can go. This is defined as the largest possible stretchable space (black outlines in diagram above).</li>
 	<li>Show patches: This highlights (in the editing pane) the stretchable areas (green) and content area (purple).</li>
 	<li>Show bad patches: When "Show patches" is ticked, this highlights (in the editing pane) whether any of your stretchable areas are "bad". An area is "bad" if the pixels vary in colour (except if they're in the opposite direction to the stretch). Your 9-patch image will still work, but is likely to not look perfect. <a href="https://stackoverflow.com/a/10964381/608312" target="_blank" rel="noopener noreferrer">This StackOverflow answer</a> contains a visual example.</li>
</ol>
</li>
</ol>
Note: The top and left bars define which areas are <strong>stretchable</strong>, but the bottom and right actually define which areas should contain <strong>content</strong>. These are often the same, but you may want them to be slightly different (e.g. to apply a bit of extra padding around the content).
<h2>Tips for converting PNGs to 9-patch</h2>
In most cases, your 9-patch will end up perfectly symmetrical (vertically &amp; horizontally), with black bars on all 4 edges and transparent corners. This will allow the edges to stretch, but not the corners. For example, here is how the earlier example looks in the editor:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Untitled.png"><img class="aligncenter size-full wp-image-2513" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/06/Untitled.png" alt="" width="456" height="254" /></a>

Make sure to check your image with "Show bad patches", to make sure no almost unnoticeable stretching issues will occur.

Additionally, don't forget to delete the original <code>.png</code> file, otherwise the build will fail due to duplicated resources.

Finally, if you'd like to look at the original image and play with the final 9-patch image, the repo used for this post is <a href="https://github.com/JakeSteam/9patch" target="_blank" rel="noopener noreferrer">available on GitHub</a>.