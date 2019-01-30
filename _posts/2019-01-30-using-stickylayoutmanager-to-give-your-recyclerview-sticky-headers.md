---
ID: 2357
post_title: >
  Using StickyLayoutManager to give your
  RecyclerView sticky headers
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-stickylayoutmanager-to-give-your-recyclerview-sticky-headers/
published: true
post_date: 2019-01-30 18:00:15
---
Once you've created a RecyclerView with headers and content, it's often useful to have the headers "sticky". Sticky headers will display over the top of your content, and help your app's users keep track of which section they are currently in.

This tutorial will cover adding sticky headers to your existing implementation in just a few lines using <a href="https://github.com/qiujayen/sticky-layoutmanager">qiujayen's Sticky-LayoutManager library</a>. If you haven't yet created a RecyclerView, I've <a href="https://blog.jakelee.co.uk/creating-a-recyclerview-with-multiple-content-types-and-layouts-in-kotlin">previously written a tutorial on creating RecyclerViews</a> with multiple content types which may be useful.

<!--more-->
<h2>Adding StickyLayoutManager library</h2>
First, add jitpack to your project-level <code>build.gradle</code>:
<pre>allprojects {
	repositories {
		<span class="pl-k">..</span>.
		maven { url <span class="pl-s"><span class="pl-pds">'</span>https://jitpack.io<span class="pl-pds">'</span></span> }
	}
}</pre>
Next, add the Sticky Layout Manager library to your app-level <code>build.gradle</code>:
<pre>dependencies {
	implementation <span class="pl-s"><span class="pl-pds">'</span>com.github.qiujayen:sticky-layoutmanager:1.0.1<span class="pl-pds">'</span></span>
}</pre>
<h2>Adding stickiness based on type</h2>
First, make your RecyclerView adapter (e.g. <code>ContentAdapter</code> in the <a href="https://blog.jakelee.co.uk/creating-a-recyclerview-with-multiple-content-types-and-layouts-in-kotlin">previous tutorial</a>) implement <code>StickyHeaders</code>:
<pre>class ContentAdapter() : RecyclerView.Adapter&lt;RecyclerView.ViewHolder&gt;(), StickyHeaders {</pre>
Next, implement the required <code>isStickyHeader(position: Int)</code> function. In this case, if a row is a <code>HeaderRow</code>, it should be sticky:
<pre>override fun isStickyHeader(position: Int) = rows[position] is HeaderRow</pre>
Finally, actually set the <code>RecyclerView</code>'s layout manager to <code>StickyHeadersLinearLayoutManager</code>, with a type of your adapter, and a context:
<pre>recyclerView.adapter = ContentAdapter()
recyclerView.layoutManager = StickyHeadersLinearLayoutManager&lt;ContentAdapter&gt;(this)</pre>
That's it! This tutorial is <a href="https://github.com/JakeSteam/StickyHeaders">available as a GitHub repo</a>.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190128-232345.png"><img class="aligncenter size-medium wp-image-2365" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190128-232345-300x257.png" alt="" width="300" height="257" /></a>