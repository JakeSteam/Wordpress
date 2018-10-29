---
ID: 1772
post_title: >
  Reducing The Size Of TextView
  DrawableStart / DrawableEnds Using
  Styles
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/reducing-the-size-of-textview-drawablestart-drawableends-using-styles/
published: true
post_date: 2018-10-17 18:14:10
---
Using image files or SVGs at the start / end of a <code>TextView</code> can be an easy way to add icons to menu items, or indicators that clicking the <code>TextView</code> will trigger an action. That being said, it can be tricky to position the drawable correctly, and it may not be possible to easily resize it (especially if it was created by someone else).

Luckily, there is a partial workaround.

<!--more-->
<h2>Implementation</h2>
Scaling the entire <code>TextView</code> smaller will decrease the size of both the text and the drawable, however the text size can then be increased to counteract this change. For example, a text size of 16 is equal to a text size of 20, when scaled to 0.8 (80%). This is because <code>20 = 16 / 0.8</code>.

This super simple formula can be expressed as:
<pre>newTextSize = originalTextSize / scale</pre>
<h2>Example</h2>
The image below shows a <code>TextView</code> with text size 16, modified using styles to have a larger and smaller <code>drawableEnd</code> than default:
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/exba22y.png" alt="exba22y" width="655" height="262" class="alignnone size-full wp-image-1773">

This was created by extracting the scaling to 3 self explanatory styles, used to scale the <code>drawableEnd</code> to 1.6x and 0.8x original size:
<pre>&lt;style name="Larger_Drawable"&gt;
&lt;item name="android:textSize"&gt;10sp&lt;/item&gt;
&lt;item name="android:scaleX"&gt;1.6&lt;/item&gt;
&lt;item name="android:scaleY"&gt;1.6&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Normal_Drawable"&gt;
&lt;item name="android:textSize"&gt;16sp&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Smaller_Drawable"&gt;
&lt;item name="android:textSize"&gt;20sp&lt;/item&gt;
&lt;item name="android:scaleX"&gt;0.8&lt;/item&gt;
&lt;item name="android:scaleY"&gt;0.8&lt;/item&gt;
&lt;/style&gt;
</pre>
In case it helps, the exact layout xml used is similarly self explanatory:
<pre>&lt;TextView
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:text="Large"
android:layout_margin="10dp"
android:drawableEnd="@drawable/ic_smiley"
style="@style/Larger_Drawable"/&gt;

&lt;TextView
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:text="Normal"
android:layout_margin="10dp"
android:drawableEnd="@drawable/ic_smiley"
style="@style/Normal_Drawable"/&gt;

&lt;TextView
android:layout_width="wrap_content"
android:layout_height="wrap_content"
android:text="Small"
android:layout_margin="10dp"
android:drawableEnd="@drawable/ic_smiley"
style="@style/Smaller_Drawable"/&gt;
</pre>
<h2>Conclusion</h2>
While this workaround does work, it definitely isn't ideal! If at all possible, the original drawable should be resized to fit in the target area. That being said, the example above is relatively clean, allowing a style to resize the drawable instead of multiple modifiers.