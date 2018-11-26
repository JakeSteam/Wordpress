---
ID: 2066
post_title: >
  How To Display App Version Inside a
  Submodule
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-display-app-version-inside-a-submodule/
published: true
post_date: 2018-11-26 18:00:09
---
Usually, getting the current version code of your app is as simple as <code>BuildConfig.VERSION_CODE</code>. Easy! However, this doesn't work if you want to retrieve your app's version code whilst inside a submodule. Instead, the <em>submodule</em>'s version code is returned.

The solution is to use the package manager to get your application's <code>PackageInfo</code>. Next, the <code>versionCode</code> and <code>versionName</code> can be retrieved (if in a fragment, replace <code>this</code> with <code>activity?</code>):
<pre>try {
    val info = this.packageManager?.getPackageInfo(this.packageName, 0)
    val versionName = info?.versionName
    val versionCode = info?.versionCode
} catch (e: PackageManager.NameNotFoundException) {
    Timber.e(e)
}</pre>
Note that whilst technically a <code>NameNotFoundException</code> can be thrown, since you're looking up your own app it's a safe bet that it exists on the device! Additionally, there is a lot of other information available from the <code>PackageInfo</code> object returned, with no extra permissions required.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/info.png"><img class="aligncenter size-full wp-image-2067" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/info.png" alt="" width="502" height="524" /></a>