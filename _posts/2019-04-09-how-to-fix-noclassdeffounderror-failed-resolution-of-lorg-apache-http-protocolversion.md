---
ID: 2462
post_title: 'How to fix NoClassDefFoundError &#8220;Failed resolution of: Lorg/apache/http/ProtocolVersion&#8221;'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-fix-noclassdeffounderror-failed-resolution-of-lorg-apache-http-protocolversion/
published: true
post_date: 2019-04-09 16:00:34
---
I recently updated an older app, and changed the target API from 27 to 28, as recommended. This app is using an older (15.x.x) version of the Google Maps library. Upon trying to actually use Google Maps, I received the following crash:
<div class="stack-trace-heading collapsible">
<div class="stack-trace-caption">
<pre class="stack-trace-title">Fatal Exception: java.lang.NoClassDefFoundError
Failed resolution of: Lorg/apache/http/ProtocolVersion;</pre>
</div>
</div>
<div class="stack-frames">
<div class="stack-frames ng-star-inserted">
<div class="stack-frame layout-row developer-code">
<div class="context-cell">With a detailed message of:</div>
<div>
<pre class="stack-trace-title">Caused by java.lang.ClassNotFoundException
Didn't find class "org.apache.http.ProtocolVersion" on path: DexPathList[[zip file "/data/user_de/0/com.google.android.gms/app_chimera/m/0000003d/MapsDynamite.apk"],nativeLibraryDirectories=[/data/user_de/0/com.google.android.gms/app_chimera/m/0000003d/MapsDynamite.apk!/lib/armeabi-v7a, /data/user_de/0/com.google.android.gms/app_chimera/m/0000003d/MapsDynamite.apk!/lib/armeabi, /system/lib]]</pre>
Newer Android versions no longer include the library (Apache HTTP) Google Maps and other libraries require, leading to this problem.

The recommended solution is to either update the Google Maps dependency in the Gradle file to at least 16.1.x (or update whichever library has the issue), but there is another workaround too. Just adding a declaration that you still need the library into your manifest within the <code>&lt;application&gt;</code> tag will resolve it:
<pre class="prettyprint"><code><span class="tag">&lt;uses-library</span><span class="pln">
      </span><span class="atn">android:name</span><span class="pun">=</span><span class="atv">"org.apache.http.legacy"</span><span class="pln">
      </span><span class="atn">android:required</span><span class="pun">=</span><span class="atv">"false"</span> <span class="tag">/&gt;</span></code></pre>
Further information about <em>why</em> Google made this change is <a href="https://developer.android.com/about/versions/pie/android-9.0-changes-28#apache-p" target="_blank" rel="noopener noreferrer">available here</a>, as is an <a href="https://developers.google.com/maps/documentation/android-sdk/config#specify_requirement_for_apache_http_legacy_library" target="_blank" rel="noopener noreferrer">official recommendation</a> of the manifest approach.

</div>
</div>
</div>
</div>