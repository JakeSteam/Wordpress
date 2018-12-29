---
ID: 2292
post_title: >
  Creating custom stacked icons with
  FontAwesome
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-custom-square-icons-with-fontawesome/
published: true
post_date: 2018-12-29 16:00:50
---
<a href="https://fontawesome.com/" target="_blank" rel="noopener">FontAwesome</a> is an excellent resource for web developers, providing almost 1500 icons for free. Whilst these are often enough for your use case, sometimes additional icons are needed. For example, on my portfolio I wanted a square icon for each social media service, but only 6 out of 10 services had a square icon available!

In this tutorial, I'll show how to create square icons for any FontAwesome icon, as well as circular, borderless, and a few other variations. A <a href="https://gist.github.com/JakeSteam/3d1d3dd9ff88d23f26da3f61f4f83bfa">Gist of the approach is available</a>, if you'd just like to see the code.

<!--more-->

As mentioned before, here's the end goal: 10 matching icons.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons-1.png"><img class="aligncenter size-full wp-image-2294" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons-1.png" alt="" width="410" height="203" /></a>

If we're just using existing <a href="https://fontawesome.com/icons">FontAwesome icons</a>, we'd end up with the following. Note the 4 mismatching icons!<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons2.png"><img class="aligncenter size-full wp-image-2295" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons2.png" alt="" width="402" height="157" /></a>

Throughout this tutorial, the current progress of the 4 mismatched icons will be shown alongside their final forms, so that every change is immediately obvious.
<h2>Displaying initial icons</h2>
First, our starting code is an <code>&lt;a&gt;</code> tag (to link to the service), containing an <code>&lt;i&gt;</code> tag (to display the FontAwesome icon):
<pre>&lt;a href="https://stackoverflow.com/u/608312" target="_blank"&gt;
    &lt;i class="fab fa-stack-overflow"&gt;&lt;/i&gt;
&lt;/a&gt;</pre>
This displays a basic icon, in the default link colour for the page (black). The example above is for StackOverflow, when applied to all 4 icons it ends up like this:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons3.png"><img class="aligncenter size-full wp-image-2296" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons3.png" alt="" width="325" height="97" /></a>It's a start, they're all clearly much too small and mismatching though!
<h2>Preparing for stack</h2>
To prepare for the "stack" used to layer icons with a fixed background, all the icons need to be made larger, and be consistent. To do that, a few FontAwesome classes need to be applied to the <em>parent</em> element, in this case our <code>&lt;a&gt;</code>.

Our code from before now becomes:
<pre>&lt;a class="fa-stack fa-2x fa-fw" href="https://stackoverflow.com/u/608312" target="_blank"&gt;
    &lt;i class="fab fa-stack-overflow"&gt;&lt;/i&gt;
&lt;/a&gt;</pre>
<ol>
 	<li><code>fa-stack</code> prepares the icon for stacking. The most noticeable effect of this is an increase in spacing around each icon.</li>
 	<li><code>fa-2x</code> doubles the size of the icon, since they're far too small currently.</li>
 	<li><code>fa-fw</code> sets the icons to be a fixed width, necessary for them to fit in a grid of icons.</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons4.png"><img class="aligncenter size-full wp-image-2297" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons4.png" alt="" width="283" height="156" /></a>We're already pretty close! Just need to add the stacked background, and our icons will be ready to go.
<h2>Implementing stack</h2>
To implement the stack, a "2x size" and "1x size" icon needs to be defined. In our case, our service icon needs to become 1x, this is done by adding the <code>fa-stack-1x</code> class to it.

Additionally, it needs inverting (since the background colour is going to be the current text colour), so <code>fa-inverse</code> is also added to the classes.

Finally, a brand new element needs adding; the background. In this example we're using <code>fa-square</code> behind the icon, so the background is placed before the icon in the code hierarchy. This background also has <code>fa-stack-2x</code>, since it's going to be larger. The final result is:
<pre>&lt;a class="fa-stack fa-2x fa-fw" href="https://stackoverflow.com/u/608312" target="_blank"&gt;
    &lt;i class="fas fa-square fa-stack-2x"&gt;&lt;/i&gt;
    &lt;i class="fab fa-stack-overflow fa-stack-1x fa-inverse"&gt;&lt;/i&gt;
&lt;/a&gt;</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons5.png"><img class="aligncenter size-full wp-image-2298" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons5.png" alt="" width="300" height="147" /></a>

All done! A <a href="https://gist.github.com/JakeSteam/3d1d3dd9ff88d23f26da3f61f4f83bfa" target="_blank" rel="noopener">Gist of my portfolio's implementation</a> is available if you'd like to see the final version.
<h2>Alternative implementations</h2>
<h3>All squares</h3>
Using the stack technique for all icons does improve consistency, both in the UI and in the code. For example, replacing <code>fa-linkedin</code> with <code>fa-linkedin-in</code> or <code>fa-github-square</code> with <code>fa-github</code>, then performing all the other steps. However, some services (only IMDb in my case) doesn't have a non-square icon! Here's the 10 services, with all the existing square icons converted to stacked square icons:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons6.png"><img class="aligncenter size-full wp-image-2299" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons6.png" alt="" width="366" height="137" /></a>
<h3>All circles</h3>
Replacing all <code>fa-square</code> backgrounds with <code>fa-circle</code> results in the following. Note the IMDb inability to play nicely!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons7.png"><img class="aligncenter size-full wp-image-2300" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons7.png" alt="" width="370" height="159" /></a>
<h3>Tower</h3>
To create a "tower" of icons, with no gaps, just replace <code>fa-square</code> with <code>fa-square-full</code> and a <code>&lt;br&gt;</code> between each icon:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons8.png"><img class="aligncenter size-full wp-image-2301" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons8.png" alt="" width="72" height="339" /></a>
<h3>Other shapes</h3>
Most <a href="https://fontawesome.com/icons?d=gallery&amp;c=shapes" target="_blank" rel="noopener">FontAwesome shapes</a> work well as a background. Here's <code>fa-</code> <code>play</code> / <code>certificate</code> / <code>comment</code> / <code>heart</code> / <code>cloud</code>, most of which look pretty good!<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons9.png"><img class="aligncenter size-full wp-image-2302" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icons9.png" alt="" width="387" height="76" /></a>
<h3>Further modifications</h3>
Since these icons are using FontAwesome, the usual modifiers can be used, simply by applying additional classes:
<ul>
 	<li><a href="https://fontawesome.com/how-to-use/on-the-web/styling/rotating-icons">Rotating / flipping</a>: <code>fa-rotate-90</code> / <code>180</code> / <code>270</code>, <code>fa-flip-horizontal</code> / <code>vertical</code>.</li>
 	<li><a href="https://fontawesome.com/how-to-use/on-the-web/styling/animating-icons">Spinning</a>: <code>fa-spin</code>.</li>
</ul>
<h2>Summary</h2>
As mentioned before, a <a href="https://gist.github.com/JakeSteam/3d1d3dd9ff88d23f26da3f61f4f83bfa" target="_blank" rel="noopener">Gist containing this tutorial's code is available</a>. Making all brand icons on your site fit a common design helps them feel like a more natural part of your site, instead of just stuck on top. A live implementation is available at the <a href="https://jakelee.co.uk">top of my portfolio</a>.

It's also worth mentioning <a href="https://fontawesome.com/pro" target="_blank" rel="noopener">FontAwesome's Pro offering</a>. This provides nearly 3x as many icons, such as outlined and light versions of the square / circle backgrounds. For $60/yr it's unlikely to be worth it for a single site, but if web developing at all professionally, it's likely worth it!

&nbsp;