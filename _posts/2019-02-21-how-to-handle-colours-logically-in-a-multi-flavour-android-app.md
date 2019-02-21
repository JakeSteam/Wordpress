---
ID: 2381
post_title: >
  How to handle colours logically in a
  multi-flavour Android app
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-handle-colours-logically-in-a-multi-flavour-android-app/
published: true
post_date: 2019-02-21 17:05:13
---
Handling colours in Android apps is generally pretty straightforward, as simple hex codes are used. However, standardising these across the entire app can be an easily ignored task, one which is essential for multi-flavour apps. Correctly utilising colours allows a single codebase to produce app flavours with radically different colours, e.g. to match company branding.

These article will walk through 4 "levels" of colour abstraction, in order from worst to best. A simple level 4 project using 2 app flavours is <a href="https://github.com/JakeSteam/MultiFlavourColours" target="_blank" rel="noopener">available as a GitHub repo</a>. Please note that these levels are by no means a standard, they're based entirely on my own experience.

<!--more-->
<h2>Level 1: Colour codes in layouts</h2>
Just putting hex colour codes in your layout is the easiest way to immediately see results, but isn't at all sustainable. If you want to change a colour in the future, you'll have to find and replace every instance of  the hex code, and make sure there's not any other uses of that hex code. Not at all reliable! It's also not possible to provide different colours for different app flavours.
<pre>&lt;TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="I'm just using the hex codes!"
        android:background="#FFFFFF"
        android:textColor="#00000"/&gt;</pre>
<h2>Level 2: Colour names in layouts</h2>
A massive improvement over level 1 is extracting all colours to a dedicated <code>colors.xml</code> file. This ensures that if you decide to change a colour in the future, at least all instances of it will be changed. Additionally, app flavours can now be used to give different definitions for a colour. For example, flavour A can define "white" as a different hex code to flavour B, just by placing a new <code>colors.xml</code> in that flavour's source set.

However, "white" still has to be... white, unless you want to end up with very confusing layouts! The next level will improve on this approach.

More information on product flavors is available in <a href="https://developer.android.com/studio/build/build-variants#product-flavors" target="_blank" rel="noopener">the official documentation</a>.
<pre>&lt;TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="I'm using colors.xml!"
        android:background="@color/white"
        android:textColor="@color/black"/&gt;</pre>
<pre>&lt;resources&gt;
    &lt;color name="white"&gt;#FFFFFF&lt;/color&gt;
    &lt;color name="black"&gt;#000000&lt;/color&gt;
&lt;/resources&gt;</pre>
<h2>Level 3: Purpose names in layouts</h2>
An improvement essential to any multi-flavour project that has multiple colour schemes is nesting colour names, with the <em>purpose-named</em> colour referencing a <em>colour-named</em> colour. This can be tricky to explain textually, an example will make more sense:
<pre>&lt;TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="I'm using purpose names!"
        android:background="@color/textBackground"
        android:textColor="@color/textForeground"/&gt;</pre>
<pre>&lt;resources&gt;
    &lt;color name="white"&gt;#FFFFFF&lt;/color&gt;
    &lt;color name="black"&gt;#000000&lt;/color&gt;
    &lt;color name="textBackground"&gt;@color/white&lt;/color&gt;
    &lt;color name="textForeground"&gt;@color/black&lt;/color&gt;
&lt;/resources&gt;</pre>
This may seem counter-intuitive, but it has a major advantage: <code>textBackground</code> can now be defined in an app flavour's <code>colors.xml</code> as any colour, whilst still ensuring a pre-defined palette is stuck to.

For this approach, the <code>colors.xml</code> file must be split into two parts:
<ol>
 	<li>A limited list of colours, with hex codes, for the flavour's internal palette. These should <strong>not</strong> be defined in any layouts, and only be referenced by other colours. An easy way to ensure this is to give them colours unique to that flavour (e.g. <code>MyCompanyWhite</code>), as other flavours won't contain this colour so will fail to build.</li>
 	<li>A lengthy list of areas that need colouring, referencing the internal palette colours. These should be used in all layouts, and it's absolutely fine if multiple colours reference the same internal palette colour.</li>
</ol>
For example, <code>textColour</code>, <code>titleColour</code>, and <code>subtitleColour</code> may all reference <code>MyCompanyBlue</code>, defined as <code>#003791</code>. Whilst <code>MyCompany</code> wants them all the same colour, <code>OtherCompany</code> may want titles a different colour. With this approach, this can be accomplished easily without impacting any other flavours of the app.

In a current project of mine, the company's internal palette consists of ~10 colours. These definitions are used for over 150 areas that need colouring, allowing fine-grained control over app theming. Make sure to name your colours according to a predefined system, or it'll get out of control quickly!
<h2>Level 4: Use names in styles</h2>
The final level of logically storing colours is to extract them into <code>styles.xml</code>. For example, using the same TextView as before:
<pre>&lt;TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="I'm using styles!"
        style="@style/bodyText"/&gt;</pre>
<pre>&lt;resources&gt;
    &lt;color name="white"&gt;#FFFFFF&lt;/color&gt;
    &lt;color name="black"&gt;#000000&lt;/color&gt;
    &lt;color name="textBackground"&gt;@color/white&lt;/color&gt;
    &lt;color name="textForeground"&gt;@color/black&lt;/color&gt;
&lt;/resources&gt;</pre>
<pre>&lt;resources&gt;
    &lt;style name="bodyText" parent="android:Widget.TextView"&gt;
        &lt;item name="android:background"&gt;@color/textBackground&lt;/item&gt;
        &lt;item name="android:textColor"&gt;@color/textForeground&lt;/item&gt;
    &lt;/style&gt;
&lt;/resources&gt;</pre>
Now, all TextViews will use the same abstracted colours by default (so long as the style is applied). Even better, these can easily be changed without manually fixing the colours defined on them all. Styles can (and should) be used for much, much more than just colours, but this example is keeping it simple!

As a follow-on, it is possible to <a href="https://stackoverflow.com/a/3166865/608312" target="_blank" rel="noopener">make all TextViews automatically use a style</a>, very useful in some cases.
<h2>Conclusion</h2>
It's worth pointing out that these level definitions are only a pattern I've personally found to work well on multi-flavour projects. I'm always interested in alternative approaches, as I find multi-flavour development one of the most challenging but fascinating parts of Android development!

An <a href="https://github.com/JakeSteam/MultiFlavourColours">example project is available for this post</a>, which provides two different flavours with radically different colour schemes, using level 4:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/GbMRMDY.png"><img class="aligncenter wp-image-2383 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/GbMRMDY.png" alt="" width="904" height="239" /></a>