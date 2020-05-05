---
ID: 2764
post_title: >
  Avoiding a 1 pixel line on Zeplin
  exports, or why density matters
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/avoiding-a-1-pixel-line-on-zeplin-exports-or-why-density-matters/
published: true
post_date: 2020-05-05 17:30:22
---
Recently I needed to update some marketing images in an Android app, based on our designer's Zeplin screens.

Exporting &amp; using these images is usually no problem, but this time our QA reported the images had an unusual white line at the bottom or right on some devices! I manually fixed them in PhotoShop, then hunted for the root cause...

<!--more-->

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/FYR8IXi.png"><img class="aligncenter size-full wp-image-2765" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/FYR8IXi.png" alt="" width="811" height="309" /></a>
<h2>Densities</h2>
To understand why this happens, we first need to quickly understand <a href="https://developer.android.com/training/multiscreen/screendensities" target="_blank" rel="noopener noreferrer">Android's densities</a>. Different devices have differences in quality &amp; size of screen, so to make a 2cm wide image on one device might need 50 pixels, on another it might need 250. <a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/devices-density_2x.png"><img class="aligncenter wp-image-2766 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/devices-density_2x.png" alt="" width="1000" height="380" /></a>

This is why we package difference <strong>densities</strong> of every image, so we can display smaller images on smaller / lower resolution devices, and vice versa. On Android we primarily deal with the following 5 densities, which can all be expressed as multiples of each other: mdpi(1x), hdpi (1.5x), xhdpi (2x), xxhdpi (3x), and xxxhdpi (4x).
<h2>The problem</h2>
The specific asset we were using had a resolution of 3072x444 pixels at the highest density, that is then downscaled for our various densities:
<ul>
 	<li>xxxhdpi: 3072x444</li>
 	<li>xxhdpi: 2304x333</li>
 	<li>xhdpi: 1536x222</li>
 	<li><strong>hdpi: 1152x166.5</strong></li>
 	<li>mdpi: 768x111</li>
</ul>
Notice the non-whole number there? That's a problem!

When Zeplin is generating this asset for export, it rounds the size up to 1152x167. However, it has no data for what to put in there so leaves it as a transparent pixel. This is usually fine, unless you're exporting as as a JPG without transparency, in which case you'll end up with... our white pixel row / column.
<h2>The solution</h2>
Luckily, this is really, really easy to fix. If our xxxhdpi asset was instead 3072x440, we'd now have the much nicer:
<ul>
 	<li>xxxhdpi: 3072x440</li>
 	<li>xxhdpi: 2304x330</li>
 	<li>xhdpi: 1536x220</li>
 	<li><strong>hdpi: 1152x165</strong></li>
 	<li>mdpi: 768x110</li>
</ul>
Now when we export our assets, we end up with the following (old on top, new on bottom), notice the lack of white pixel?

&nbsp;

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/I7zOvZi.png"><img class="aligncenter wp-image-2767 size-large" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/05/I7zOvZi-1024x373.png" alt="" width="700" height="255" /></a>

There we go! So long as the mdpi asset has an even number of pixels in it's width and height, there'll be no problems.