---
ID: 1686
post_title: >
  Using multi-weighted custom fonts on
  Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-multi-weighted-custom-fonts-on-android/
published: true
post_date: 2018-09-10 18:33:37
---
Many apps can use the default Android font, Roboto. However, often clients will have a branded font that must be used that is not included in Android. Luckily, <a href="https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml" target="_blank" rel="noopener">XML fonts</a> (API 16+) solve this issue very neatly. However, these fonts can only be bold, or not bold, yet many fonts have semibold / semilight variants that need to be supported.

<!--more-->
<h3>Prerequisites</h3>
For this example, the following 4 weights (and 4 italic versions) of <a href="https://www.lucasfonts.com/fonts/thesans/thesans/overview/" target="_blank" rel="noopener">The Sans C4s</a> will be used, but of course any custom font can be used:
<ol>
 	<li>Semilight (+italic)</li>
 	<li>Regular (+italic)</li>
 	<li>Semibold (+italic)</li>
 	<li>Bold (+italic)</li>
</ol>
<code>.otf</code> versions of each variant were placed in <code>app/src/main/res/font</code>, the same place the XML descriptors will be placed.
<h3>Font XMLs</h3>
The core idea is to have two XML fonts defined; one for "semi" weights (semilight and semibold), and one for "regular" weights (regular and bold).

The first file, <code>the_sans_c4s.xml</code> is for "regular" weights:
<pre>&lt;font-family
    xmlns:app="http://schemas.android.com/apk/res-auto"&gt;
    &lt;font
        app:fontStyle="normal"
        app:fontWeight="400"
        app:font="@font/the_sans_c4s_regular"/&gt;

    &lt;font
        app:fontStyle="italic"
        app:fontWeight="400"
        app:font="@font/the_sans_c4s_regular_italic"/&gt;

    &lt;font
        app:fontStyle="normal"
        app:fontWeight="700"
        app:font="@font/the_sans_c4s_bold"/&gt;

    &lt;font
        app:fontStyle="italic"
        app:fontWeight="700"
        app:font="@font/the_sans_c4s_bold_italic"/&gt;
&lt;/font-family&gt;</pre>
The second, <code>the_sans_c4s_semi.xml</code> is for "semi" weights:
<pre>&lt;font-family
    xmlns:app="http://schemas.android.com/apk/res-auto"&gt;
    &lt;font
        app:fontStyle="normal"
        app:fontWeight="400"
        app:font="@font/the_sans_c4s_semilight"/&gt;

    &lt;font
        app:fontStyle="italic"
        app:fontWeight="400"
        app:font="@font/the_sans_c4s_semilight_italic"/&gt;

    &lt;font
        app:fontStyle="normal"
        app:fontWeight="700"
        app:font="@font/the_sans_c4s_semibold"/&gt;

    &lt;font
        app:fontStyle="italic"
        app:fontWeight="700"
        app:font="@font/the_sans_c4s_semibold_italic"/&gt;
&lt;/font-family&gt;</pre>
<h3>Styles</h3>
Now that the basic fonts are ready to use (via <code>android:fontFamily</code>), a very convenient addition is a style for each variant. This allows easily setting a font variant on any element using <code>style="Font_SemiBold_Italic"</code>, instead of remembering the right combination of <code>textStyle</code> and <code>fontFamily</code>.

The full styleset is included below:
<pre>&lt;style name="Font_Regular" tools:keep="@style/Font_Regular"&gt;
&lt;item name="android:fontFamily"&gt;@font/the_sans_c4s&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_Regular_Italic" parent="Font_Regular" tools:keep="@style/Font_Regular_Italic"&gt;
&lt;item name="android:textStyle"&gt;italic&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_Bold" parent="Font_Regular" tools:keep="@style/Font_Bold"&gt;
&lt;item name="android:textStyle"&gt;bold&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_Bold_Italic" parent="Font_Regular" tools:keep="@style/Font_Bold_Italic"&gt;
&lt;item name="android:textStyle"&gt;bold|italic&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_SemiLight"&gt;
&lt;item name="android:fontFamily"&gt;@font/the_sans_c4s_semi&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_SemiLight_Italic" parent="Font_SemiLight" tools:keep="@style/Font_SemiLight_Italic"&gt;
&lt;item name="android:textStyle"&gt;italic&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_SemiBold" parent="Font_SemiLight" tools:keep="@style/Font_SemiBold"&gt;
&lt;item name="android:textStyle"&gt;bold&lt;/item&gt;
&lt;/style&gt;

&lt;style name="Font_SemiBold_Italic" parent="Font_SemiLight"&gt;
&lt;item name="android:textStyle"&gt;bold|italic&lt;/item&gt;
&lt;/style&gt;</pre>
Additionally, as these are just simple styles, any existing styles can just set them as a parent to easily customise the font used.
<h3>Conclusion</h3>
Whilst the vast majority of this article is about implementing the <a href="https://developer.android.com/guide/topics/ui/look-and-feel/fonts-in-xml">existing fonts functionality</a>, extending this to support multiple weights can initially be daunting, since Android only supports <code>bold</code> and/or <code>italic</code>. Hopefully this brief write-up helps others with similar issues.

A <a href="https://gist.github.com/JakeSteam/d9ae8eb85e2c37da8d4648f118609418" target="_blank" rel="noopener">Gist of this post</a> is also available.