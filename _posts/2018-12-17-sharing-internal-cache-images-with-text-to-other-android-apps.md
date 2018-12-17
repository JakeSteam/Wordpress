---
ID: 2220
post_title: >
  Sharing internal / cache images (with
  text) to other Android apps
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/sharing-internal-cache-images-with-text-to-other-android-apps/
published: true
post_date: 2018-12-17 19:08:12
---
Often, users of your app will want to share images from it. Whether this is their own content, their friend's content, or content published by you to your users, sharing can massively help your app's popularity. This can be a little tricky if you're displaying images from inside your app's directory, since these files aren't accessible from other apps! This means that when a user tries to share the image, the app it is shared to will be unable to access it.

The solution to this is creating a <code>FileProvider</code> that makes your images' directory accessible. Then, forming an intent that grants the app that was shared to permission to view the image.<!--more-->

The code used in this tutorial was used to add image sharing to my upcoming <a href="https://github.com/JakeSteam/APODWallpaper">Astronomy Picture of the Day app</a>. All code used is also <a href="https://gist.github.com/JakeSteam/3a685edee460ad2497fd3827b70622df">available as a GitHub Gist</a>, if you just want to skip to copy and pasting code!
<h2>Configuring file paths XML</h2>
First, a file needs to be created to define which internal files should be shared. The XML below should be saved as <code>filepaths.xml</code> inside <code>/res/xml/</code>, and makes the <code>/cache/images/</code> directory available:
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;paths&gt;
    &lt;cache-path name="shared_images" path="/images/"/&gt;
&lt;/paths&gt;</pre>
Note that if you are using <code>.filesDir</code> and not <code>.cacheDir</code> in your sharing code, <code>&lt;files-path</code> must be used instead of <code>&lt;cache-path</code>.
<h2>Adding FileProvider to manifest</h2>
The <code>FileProvider</code> library needs to be added to your project. If you are using AndroidX, it is included already. If you are not, make sure to add the following dependency to your app-level <code>build.gradle</code>:
<pre>compile 'com.android.support:support-v4:28.0.0'</pre>
Now that our directory to share has been defined, it has to be added to the <code>AndroidManifest.xml</code>, inside the <code>&lt;application&gt;</code> tag. Note that this example uses AndroidX, for non-AndroidX projects the <code>name</code> should be set to <code>android.support.v4.content.FileProvider</code> instead.
<pre>&lt;provider
        android:name="androidx.core.content.FileProvider"
        android:authorities="${applicationId}.fileprovider"
        android:grantUriPermissions="true"
        android:exported="false"&gt;
    &lt;meta-data
            android:name="android.support.FILE_PROVIDER_PATHS"
            android:resource="@xml/filepaths" /&gt;
&lt;/provider&gt;</pre>
<h2>Adding image sharing function</h2>
To actually share the image, first the file's URI is retrieved using the previously defined authority and <code>getUriForFile</code>. Next, an intent is created with the following parameters set:
<ul>
 	<li><code>setAction</code>: Determines the intent is for sending content.</li>
 	<li><code>addFlags</code>: Gives the receiving app permission to view the attached image.</li>
 	<li><code>setDataAndType</code>: This set the image's URI, as well as the filetype (e.g. PNG / JPG).</li>
 	<li><code>putExtra(Intent.EXTRA_STREAM</code>: This adds the URI as a streamable data source.</li>
 	<li><code>putExtra(Intent.EXTRA_TEXT</code>: This sets the text to be shared alongside the image (where supported).</li>
</ul>
This intent is then passed to <code>Intent.createChooser</code>, along with a string which <em>may</em> be shown (depending on device).
<pre>fun shareImage(file: File) {
    val authority = "${BuildConfig.APPLICATION_ID}.fileprovider"
    FileProvider.getUriForFile(context, authority, file)?.let {
        val shareIntent = Intent()
            .setAction(Intent.ACTION_SEND)
            .addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
            .setDataAndType(it, context.contentResolver.getType(it))
            .putExtra(Intent.EXTRA_STREAM, it)
            .putExtra(Intent.EXTRA_TEXT, "I'm sharing this image!")
        context.startActivity(Intent.createChooser(shareIntent, "Share image to:"))
    }
}</pre>
<h2>Sharing the image</h2>
To share your image, all you need is a <code>File</code> object of it to be passed to the <code>shareImage</code> function. This can be done using the <code>File(directory, child)</code> constructor:
<pre>val imageCache = File(context.cacheDir, "images")
shareImage(File(imageCache, "myimage.png"))</pre>
Note that I'm using the cache directory in this example, <code>.filesDir</code> can be used instead (make sure to change your <code>filepaths.xml</code> too!).

When your image sharing function is called, the user will receive a popup suggesting many, many locations to share their content to. A screenshot from newly created emulator is below, real devices will have many pages of options instead!

All code in this tutorial is available as a Gist.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/sharing2.png"><img class="aligncenter size-full wp-image-2224" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/sharing2.png" alt="" width="400" height="479" /></a>

&nbsp;