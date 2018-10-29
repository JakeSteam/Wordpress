---
ID: 1792
post_title: >
  Using Fabric (Answers / Beta /
  Crashlytics) With App Flavours
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-fabric-answers-beta-crashlytics-with-app-flavours/
published: true
post_date: 2018-10-25 15:00:07
---
Setting up Fabric is usually pretty straightforward, especially as they have a <a href="https://fabric.io/kits/android/crashlytics/install" target="_blank" rel="noopener">step by step guide</a>, and even an <a href="https://fabric.io/downloads/android-studio" target="_blank" rel="noopener">Android Studio plugin</a> that can do it all for you.

Initialising Fabric is also just a simple one-liner in your application class' <code>onCreate()</code>:
<pre>
      Fabric.with(this, new Crashlytics());</pre>
Easy, run the app and we're done! Except.. not if we're using more than one flavour. The package names of the alternate flavours won't register, uh oh...

<!--more-->

Since Fabric automatically picks up the app identifier from the manifest, it isn't possible to manually add an alternate flavour, and even running the alternate flavour doesn't force Fabric to add it.

To get around this, the app identifier has to be manually set when Fabric is initialised, based on the current <code>BuildConfig</code>:
<pre>
        Fabric.with(
            Fabric.Builder(this)
                .kits(Crashlytics())
                .appIdentifier(BuildConfig.APPLICATION_ID)
                .build()
        )</pre>
Now, the <b>current</b> application ID will be registered, instead of the package used in <code>AndroidManifest.xml</code>. The different flavours will be treated as completely independent apps in Fabric, so will have different crash reports, Beta release groups, etc.