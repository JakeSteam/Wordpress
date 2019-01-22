---
ID: 2349
post_title: >
  Adding a variable to both the Android
  Manifest and Build Config
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/adding-a-variable-to-both-the-android-manifest-and-build-config/
published: true
post_date: 2019-01-22 17:00:51
---
Whilst it is possible to set a variable in your <code>AndroidManifest.xml</code> using <code>manifestPlaceholders</code> and setting the value in your <code>build.gradle</code>, it can often be useful to access these values in your code too. For example, I recently used this post's technique to define a deep link path (defined in the manifest) and check the url's path at runtime (checked in the code).

Defining these variables twice is likely to cause future discrepancies, a much more reliable approach is to set them in the manifest and the code at the same time. Luckily, your build config can store custom values just as well as your manifest!

<!--more-->
<h2>Adding a new variable</h2>
The main idea behind this approach is creating a function in <code>build.gradle</code> to perform two tasks at once for us at the same time. This <code>addConstantTo</code> function should go inside your <code>defaultConfig { }</code>, and takes 2 parameters; the variable name and variable values:
<pre class="default prettyprint prettyprinted"><code><span class="pln">android </span><span class="pun">{</span><span class="pln">
    defaultConfig </span><span class="pun">{</span>
        <span class="kwd">def</span><span class="pln"> addConstant </span><span class="pun">=</span> <span class="pun">{</span><span class="pln">constantName</span><span class="pun">,</span><span class="pln"> constantValue </span><span class="pun">-&gt;</span><span class="pln">
            manifestPlaceholders </span><span class="pun">+=</span> <span class="pun">[</span> <span class="pun">(</span><span class="pln">constantName</span><span class="pun">):</span><span class="pln">constantValue</span><span class="pun">]</span><span class="pln">
            buildConfigField </span><span class="str">"String"</span><span class="pun">,</span> <span class="str">"${constantName}"</span><span class="pun">,</span> <span class="str">"\"${constantValue}\""</span>
        <span class="pun">}</span><span class="pln">
        addConstant<span class="pun">(</span><span class="str">"DEEPLINK_HOST"</span><span class="pun">,</span> <span class="str">"https://blog.jakelee.co.uk"</span><span class="pun">)</span>
        addConstant</span><span class="pun">(</span><span class="str">"DEEPLINK_PATH"</span><span class="pun">,</span> <span class="str">"/deeplink/myprofile"</span><span class="pun">)</span>
    <span class="pun">}</span></code></pre>
This can then be accessed in your manifest with <code>${NAME}</code>:
<pre>&lt;intent-filter&gt;
    &lt;action android:name="android.intent.action.VIEW" /&gt;
    &lt;category android:name="android.intent.category.DEFAULT" /&gt;
    &lt;category android:name="android.intent.category.BROWSABLE" /&gt;
    &lt;data
        android:host="${DEEPLINK_HOST}"
        android:pathPrefix="${DEEPLINK_PATH}"
        android:scheme="https" /&gt;
&lt;/intent-filter&gt;</pre>
It can also be accessed in your runtime code at anytime (Kotlin example):
<pre>fun isDeeplink() = arguments.data.encodedPath == BuildConfig.DEEPLINK_PATH</pre>
<h2>Adding a flavor / build type dependant variable</h2>
This approach unfortunately won't work by default if you utilise flavors or build types in your app. For this you need to add a <code>target</code> parameter to your function, inside <code>buildTypes { }</code> or <code>productFlavors { }</code>:
<pre>def addConstantTo = {target, constantName, constantValue -&gt;
    target.manifestPlaceholders += [ (constantName):constantValue]
    target.buildConfigField "String", "${constantName}", "\"${constantValue}\""
}</pre>
This new <code>target</code> parameter should always be set to <code>owner</code>, the behaviour of other two parameters is unchanged. For example, here's a usage within product flavors:
<pre>productFlavors {
    def addConstantTo = {target, constantName, constantValue -&gt;
        target.manifestPlaceholders += [ (constantName):constantValue]
        target.buildConfigField "String", "${constantName}", "\"${constantValue}\""
    }
    flavorone{
        addConstantTo(owner, "DEEPLINK_HOST", "one.example.com")
    }
    flavortwo{
        addConstantTo(owner, "DEEPLINK_HOST", "two.example.com")
    }</pre>
Hopefully this makes it a bit easier to use flavor / build type specific variables in your apps. A common use is safely setting debug and release remote server URLs. Since the function just performs the same two actions as setting them manually, it's unlikely to cause any side effects. Note that if you want to store anything besides a <code>String</code> inside your <code>BuildConfig</code> using this you'll need to further modify the function.

Many thanks to <a href="https://stackoverflow.com/a/40592469/608312" target="_blank" rel="noopener">TmTron on StackOverflow for this solution</a>, it's definitely an underappreciated and elegant solution!