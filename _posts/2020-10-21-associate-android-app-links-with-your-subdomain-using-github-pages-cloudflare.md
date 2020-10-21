---
ID: 2929
post_title: 'Associate Android app links with your subdomain using GitHub Pages &#038; Cloudflare'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/associate-android-app-links-with-your-subdomain-using-github-pages-cloudflare/
published: true
post_date: 2020-10-21 15:00:21
---
As part of my <a href="https://github.com/JakeSteam/Apod-Wallpaper-2/" target="_blank" rel="noopener noreferrer">APOD wallpaper rewrite</a>, I wanted to support universal links / app links. These allow users with the app installed to click a link like <code>apod.jakelee.co.uk/day/2020-01-01</code> and be taken directly to that APOD within the app!

However, to complicate matters slightly my domain is behind Cloudflare, I wanted to use a subdomain, and I wanted to use GitHub pages for free hosting. This introduced a few problems, hopefully this post helps others solve them!

<!--more-->

As a very brief overview of the process, we'll be hosting a config file that tells Google / Android our app is allowed to automatically handle our links.
<h2>Starting resources</h2>
Before we get started, I'd like to mention <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://apod.jakelee.co.uk&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">this extremely helpful URL</a> I found whilst implementing app links. The response shows what Google uses when they try to validate your links, along with any errors encountered. To check your site, just replace <code>apod.jakelee.co.uk</code> with your URL (you can also look at <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://facebook.com&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">Facebook's</a> or <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://google.com&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">Google's</a>!).

Throughout this tutorial we'll also be using the "App Links Assistant", available under "Tools" in Android Studio. There's <a href="https://developer.android.com/studio/write/app-link-indexing" target="_blank" rel="noopener noreferrer">a very detailed companion guide</a>, containing excellent information on handling the links and generating the asset linking file.

I'd also recommend reading through the <a href="https://developer.android.com/training/app-links" target="_blank" rel="noopener noreferrer">App Links training</a>, specifically <a href="https://developer.android.com/training/app-links/verify-site-associations" target="_blank" rel="noopener noreferrer">verifying links</a>.
<h2>Preparing your JSON linking file</h2>
First, to tell Android that our app is allowed to handle certain URLs, we need to host an <code>assetlinks.json</code> file. This essentially lists our URL, our app's package, and our app's signature.

This can be made manually, but Android Studio's wizard (Tools -&gt; App Links Assistant -&gt; Associate website) is much easier.

Simply enter your domain and package, along with your signing information (to retrieve the certificate), and the file will be made for you. Save it somewhere, and we'll move on to hosting it.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/fmBaJx6.png"><img class="aligncenter size-full wp-image-2933" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/fmBaJx6.png" alt="" width="968" height="686" /></a>

&nbsp;
<h2>Making a GitHub pages repository</h2>
Next, we need to make and configure a repository to host our file (free hosting!). <a href="https://github.com/JakeSteam/APODWallpaperSubdomain" target="_blank" rel="noopener noreferrer">Here's my repository</a> as an example.
<ol>
 	<li>Create a new public GitHub repository, and clone it locally.</li>
 	<li>Put your newly generated <code>assetlinks.json</code> file in <code>/.well-known/</code>, commit &amp; push.</li>
 	<li>In the repository's settings, set a "Source" for GitHub pages (probably master / main branch).</li>
 	<li>Enter your custom domain's information.</li>
</ol>
Your settings should look like this, note that "Enforce HTTPS" won't be available yet, and your site won't be published yet.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/SBMV43c.png"><img class="aligncenter size-full wp-image-2938" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/SBMV43c.png" alt="" width="935" height="622" /></a>

&nbsp;
<h2>Setting up DNS in Cloudflare</h2>
Now our file is hosted, we need to actually make it available at our URL!

Configuring the DNS records to properly display the GitHub pages repository was perhaps the trickiest bit of this process. There is <a href="https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-a-subdomain" target="_blank" rel="noopener noreferrer">an official GitHub walkthrough</a>, but the process simply didn't work for me, perhaps because I use Cloudflare.

Here's how I got it working:
<ol>
 	<li>In Cloudflare, turn on "Development Mode" under "Quick Actions" on the overview of your domain. This makes sure all changes are visible immediately.</li>
 	<li>On GitHub's walkthrough, scroll to Step 4 of "<a href="https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site#configuring-an-apex-domain" target="_blank" rel="noopener noreferrer">Configuring an apex domain</a>". This contains the IP addresses we need.</li>
 	<li>Using at least 2 of those IPs, add proxied "A" records for your subdomain. It will end up looking like this:</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/aLyRemB.png"><img class="aligncenter size-large wp-image-2935" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/aLyRemB-1024x248.png" alt="" width="700" height="170" /></a>

When you visit your subdomain's URL, you should now see some information about your GitHub repository. Whilst it can take up to 24 hours for these changes to propagate, for me it was just a few seconds!
<h2>Final changes on GitHub</h2>
Now your subdomain is configured, enable the "Enforce HTTPS" option now available in your GitHub repo's settings. This needs to be enabled for app linking to work, but can take a few minutes to appear.

I got stuck here for quite a while, as it turns out GitHub treats folders in Pages repositories differently to how I expected. Essentially, you can't navigate directly to your asset linking file without telling GitHub the folder should be included:
<ol>
 	<li>Create the file <code>_config.yml</code> in the root of your project.</li>
 	<li>Put <code>include: [".well-known"]</code> inside, and save.</li>
</ol>
Visit your asset links URL (https://yourdomain/.well-known/assetlinks.json), and you should finally see your file!

Finally, check your domain on <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://apod.jakelee.co.uk&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">Google's asset linking API</a>. You should see your json, followed by <code>**** ERRORS ****\nNone!\n</code>, this means it's all sorted!

It can take a while for Cloudflare and GitHub changes to propagate, so be prepared to wait a few hours. In the meantime, we can make the necessary changes to the app.
<h2>Adding URLs to Android manifest</h2>
The only change we need to make to the manifest is specifying which activity our URL should open. We also want to use <code>autoVerify</code> to automatically verify we own the domain.

This can be done automatically in Android Studio's Tools -&gt; App Links Assistant -&gt; URL Mapping Editor:
<ol>
 	<li>Click the add button (+)</li>
 	<li>Enter your URL</li>
 	<li>Pick an activity (probably <code>MainActivity</code>)</li>
 	<li>Press OK</li>
</ol>
An intent filter will then be added to your <code>AndroidManifest.xml</code> looking something like:
<pre>&lt;intent-filter android:autoVerify="true"&gt;
    &lt;action android:name="android.intent.action.VIEW" /&gt;
    &lt;category android:name="android.intent.category.DEFAULT" /&gt;
    &lt;category android:name="android.intent.category.BROWSABLE" /&gt;
    &lt;data android:scheme="https" android:host="apod.jakelee.co.uk" /&gt;
&lt;/intent-filter&gt;</pre>
Your screen will end up looking like this, you can click a URL to see it in the manifest:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/I0oX3qx.png"><img class="aligncenter size-full wp-image-2936" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/10/I0oX3qx.png" alt="" width="863" height="868" /></a>

&nbsp;
<h2>Conclusion</h2>
That's it, your app is linked to your domain!

Now, when you try to open links to your domain, your app should automatically open. If this isn't the case, make sure:
<ul>
 	<li>You're signing your app with the same certificate / key you used to create your <code>assetlinks.json</code>.</li>
 	<li>You don't see any errors when checking your URL <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://apod.jakelee.co.uk&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">on the API</a>.</li>
 	<li>You've tried completely reinstalling your app, to force the verification process.</li>
 	<li>You're using HTTPS (not HTTP) everywhere.</li>
 	<li>You've read through <a href="https://developer.android.com/studio/write/app-link-indexing" target="_blank" rel="noopener noreferrer">the official tutorial</a>, and there's no edge cases there that apply to you.</li>
</ul>
The next steps are actually handling the intent that a universal link will send to your <code>Activity</code>. There's some starting information in the <a href="https://developer.android.com/studio/write/app-link-indexing#handling" target="_blank" rel="noopener noreferrer">official App Links tutorial</a>, I'll also be making a follow up post about supporting universal links and (outdated) custom schema links (e.g. apod://x).
<h2>Further resources</h2>
<ul>
 	<li>App links guide: <a href="https://developer.android.com/training/app-links" target="_blank" rel="noopener noreferrer">https://developer.android.com/training/app-links</a></li>
 	<li>Verifying app links guide: <a href="https://developer.android.com/training/app-links/verify-site-associations" target="_blank" rel="noopener noreferrer">https://developer.android.com/training/app-links/verify-site-associations</a></li>
 	<li>Official app links tutorial: <a href="https://developer.android.com/studio/write/app-link-indexing" target="_blank" rel="noopener noreferrer">https://developer.android.com/studio/write/app-link-indexing</a></li>
 	<li>My GitHub pages repository: <a href="https://github.com/JakeSteam/APODWallpaperSubdomain" target="_blank" rel="noopener noreferrer">https://github.com/JakeSteam/APODWallpaperSubdomain</a></li>
 	<li>Technical info on app links: <a href="https://chris.orr.me.uk/android-app-linking-how-it-works/" target="_blank" rel="noopener noreferrer">https://chris.orr.me.uk/android-app-linking-how-it-works/</a></li>
 	<li>GitHub pages custom domain tutorial: <a href="https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site" target="_blank" rel="noopener noreferrer">https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site</a></li>
 	<li>API to check your app linking file: <a href="https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://apod.jakelee.co.uk&amp;relation=delegate_permission/common.handle_all_urls" target="_blank" rel="noopener noreferrer">https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://apod.jakelee.co.uk&amp;relation=delegate_permission/common.handle_all_urls</a></li>
</ul>