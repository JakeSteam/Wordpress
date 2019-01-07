---
ID: 2322
post_title: >
  Using break strategy to fix unusual
  Android TableRow text wrapping issues
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-break-strategy-to-fix-unusual-android-text-wrapping-issues/
published: true
post_date: 2019-01-07 18:00:20
---
An easy way of creating simple, aligned layouts in XML is using a TableLayout and TableRows. This works excellently for most situations, but unfortunately has a few quirks when it comes to handling TextView text wrapping. Recently a bug report was received where very long emails addresses weren't displayed correctly, and flowed offscreen.

This post will cover how to fix the text flowing off screen, and the subsequent wrapping issue encountered. Just for reference, this is how the TextView in a TableRow looked when populated with a long word (in this case an email address):

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-004708.png"><img class="aligncenter size-medium wp-image-2323" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-004708-300x168.png" alt="" width="300" height="168" /></a><!--more-->

A <a href="https://gist.github.com/JakeSteam/46b0bd81d50d390a3bf6ddd1db8aacde" target="_blank" rel="noopener">GitHub gist of the solution</a> is also available.
<h2>Fixing text escaping row</h2>
Fixing the text flowing off-screen was a simple matter of setting <code>android:layout_weight="1"</code> on the TextView I wanted to wrap, e.g.:
<pre>&lt;TableRow
    android:layout_width="match_parent"
    android:layout_height="match_parent"&gt;
    &lt;TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/Label_Email" /&gt;

    &lt;TextView
        android:id="@+id/customer_email"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1" /&gt;
&lt;/TableRow&gt;</pre>
The TableLayout itself should also have <code>android:stretchColumns="*"</code> (or your specific column index), otherwise this fix is less likely to work.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-005308.png"><img class="aligncenter size-medium wp-image-2324" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-005308-300x192.png" alt="" width="300" height="192" /></a>
<h2>Fixing text not filling TextView</h2>
Our text is now wrapping correctly but.. not in the way we'd expect! Despite how it looks, the TextView is correctly taking up all available space (this can be verified by viewing bounds or setting a background colour), it's the text itself misbehaving.

This is caused by Android's default <a href="https://developer.android.com/reference/android/widget/TextView.html#attr_android:breakStrategy" target="_blank" rel="noopener">break strategy</a> handling long words poorly. The break strategy determines how a word should be broken if it cannot fit all in one line, and can be <code>simple</code>, <code>balanced</code> (default) or <code>high_quality</code>. <code>balanced</code> is trying to make all lines the same length, which is not really what we want. Instead we want <code>simple</code>, to just start a new line when it's needed.

So, just adding <code>android:breakStrategy="simple"</code> to your TextView will make the text wrap as expected. However, this is only available on API levels 23+, so you may want to create a style of this in your <code>styles.xml</code> and apply it to your TextView for API levels &gt; M, so at least those versions wrap correctly:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-010215.png"><img class="aligncenter size-medium wp-image-2325" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/Screenshot_20190106-010215-300x168.png" alt="" width="300" height="168" /></a>

Here's the final XML, for completeness' sake:
<pre>&lt;TableRow
    android:layout_width="match_parent"
    android:layout_height="match_parent"&gt;
    &lt;TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/Label_Email" /&gt;

    &lt;TextView
        android:id="@+id/customer_email"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        style="@style/SimpleBreakStrategy"/&gt;
&lt;/TableRow&gt;</pre>
And the associated <code>styles.xml</code> entry:
<pre>&lt;style name="SimpleBreakStrategy"&gt;
    &lt;item name="android:breakStrategy" tools:targetApi="m"&gt;simple&lt;/item&gt;
&lt;/style&gt;</pre>
&nbsp;