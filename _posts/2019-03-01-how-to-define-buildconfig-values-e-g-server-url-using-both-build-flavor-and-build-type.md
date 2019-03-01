---
ID: 2405
post_title: >
  How to define BuildConfig values (e.g.
  server URL) using both build flavor and
  build type
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-define-buildconfig-values-e-g-server-url-using-both-build-flavor-and-build-type/
published: true
post_date: 2019-03-01 14:58:31
---
When creating an app, build variants are almost always used to some degree. For example, working on a <code>debug</code> build during development but publishing a <code>release</code> build. These <code>buildTypes</code> are a good way of distinguishing between multiple environments your app may run in. You'll often have a QA <code>buildType</code> that talks to a different server than your live app. Setting server URLs for each <code>buildType</code> is a very common practice, and is usually enough.

However, some apps have multiple flavours, such as an app that is <a href="https://blog.jakelee.co.uk/how-to-handle-colours-logically-in-a-multi-flavour-android-app/">branded with different colours</a> for multiple clients. The problem arises when these two approaches are combined, and a wildly different server URL is needed for every combination of flavour and <code>buildType</code>.

<!--more-->
<h2>Approach</h2>
There are a few different solutions to this problem, some more elegant than others. This post will cover an implementation of Alex Kucherenko's excellent <a href="https://stackoverflow.com/a/47199250" target="_blank" rel="noopener noreferrer">StackOverflow answer</a>. An example implementation project is <a href="https://github.com/JakeSteam/CombinedBuildConfigVariables" target="_blank" rel="noopener noreferrer">available on GitHub</a>.

The core idea of the approach is to define a dictionary of key-value pairs called <code>serverUrl</code> inside each product flavour. This will contain every <code>buildType</code> as a key, and the appropriate server URL as the value. Then, a function will determine the correct URL to use during the build.

In this example, there are 4 environments (<code>debug</code>, <code>qa</code>, <code>staging</code>, <code>release</code>), and 3 client company flavours (<code>companyA</code>, <code>companyB</code>, <code>companyC</code>) to build for.
<h2>Defining values</h2>
First, for each product flavour the <code>serverUrl</code> dictionary needs to be defined, containing a value for every possible build type. This should go inside the <code>android {</code> part of your app-level <code>build.gradle</code>.
<pre>flavorDimensions "company"
productFlavors {
    companyA {
        ext.serverUrl = [
                debug: "https://internal.companya-dev.com",
                qa: "https://testing.companya.com",
                staging: "https://beta.companya.com",
                release: "https://api.companya.com"
        ]
    }
    companyB {
        ext.serverUrl = [
                debug: "https://alpha.test.companyb.net",
                qa: "https://test.companyb.net",
                staging: "https://api.companyb.net",
                release: "https://api.companyb.net"
        ]
    }
    companyC {
        ext.serverUrl = [
                debug: "http://192.168.0.1:29",
                qa: "http://192.168.0.1:45",
                staging: "http://192.168.0.1:77",
                release: "https://api.companyc.com"
        ]
    }
}</pre>
<h2>Parsing values</h2>
Next, a function loops through every possible build variant, and looks up the appropriate value based on the flavour and build type. The BuildConfig constant <code>SERVER_URL</code> is then assigned this value, and can be accessed with <code>BuildConfig.SERVER_URL</code>. The function also goes inside <code>android {</code>:
<pre>applicationVariants.all { variant -&gt;
    def flavor = variant.productFlavors[0]
    variant.buildConfigField "String", "SERVER_URL", "\"${flavor.serverUrl[variant.buildType.name]}\""
}</pre>
<h2>Next steps</h2>
This approach can be used for much more than just server URLs, and the BuildConfig setting function is generic enough that new build variant specific variables can be added just by adding a new line.

Note that <a href="https://stackoverflow.com/a/30486355" target="_blank" rel="noopener noreferrer">some alternative approaches</a> use large if / else trees, by checking what the final build name starts with or contains. An issue with this is if you assume any build with "qa" in should get a QA url, what happens if you make a version for a client called "Kwiqapps"? They're always going to get QA builds! The approach in this post is safe to use, regardless of the flavour or build type name.

<em>Shoutout to David Medenjak, who posted <a href="https://blog.davidmedenjak.com/android/2016/11/09/build-variants.html" target="_blank" rel="noopener noreferrer">a very similar approach</a> I came across just as I was finishing this article!</em>

&nbsp;