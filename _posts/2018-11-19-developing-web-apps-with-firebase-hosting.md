---
ID: 2003
post_title: >
  Developing Web Apps With Firebase
  Hosting
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-web-apps-with-firebase-hosting/
published: true
post_date: 2018-11-19 18:26:09
---
Firebase hosting provides an extremely simple way to deploy JavaScript web apps. Whilst it lacks almost all of the advanced features other hosts have, it does have the major advantage of being free for low-traffic usage. This tutorial will cover creating a basic site, as well as utilising a custom subdomain with Cloudflare.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/u/0/project/_/hosting/main" target="_blank" rel="noopener">Firebase Hosting dashboard</a>, and the <a href="https://firebase.google.com/docs/hosting/" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and the hosted code is <a href="https://github.com/JakeSteam/FirebaseHosting" target="_blank" rel="noopener">available as a repository</a>. There is also <a href="https://github.com/JakeSteam/FirebaseReference/pull/7">a pull request</a> for the very minor changes made to the Android reference app.
<h3>Setting up Firebase Hosting environment</h3>
<ol>
 	<li>First <a href="https://www.npmjs.com/get-npm" target="_blank" rel="noopener">install npm &amp; node.js</a>, used to handle the Firebase installation process.</li>
 	<li>Next, open a Command Prompt and enter <code><span style="font-family: 'courier new', courier, monospace;">npm install -g firebase-tools</span></code>, after a few minutes you should see something similar to the following image:<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/2.png"><img class="wp-image-1928 size-full aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/2.png" alt="" width="1179" height="619" /></a></li>
 	<li>Next, login to your Firebase account using <code><span style="font-family: 'courier new', courier, monospace;">firebase login</span></code>. This will open a browser with a login request. Once logged in, the Command Prompt will report success.<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/yZ61qY0.png"><img class="aligncenter wp-image-1931 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/yZ61qY0.png" alt="" width="860" height="229" /></a></li>
 	<li>Next, navigate to the directory you want to deploy code from.</li>
 	<li>Enter <code>firebase init</code> to initialise a local Firebase project. You will be prompted to choose a service (choose "Hosting"), and a Firebase project to use.</li>
 	<li>Finally, pick a folder to use for your public files (default is "public"), and whether you want a single page web app (this can be changed later).<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/NA55Gff.png"><img class="aligncenter size-full wp-image-2005" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/NA55Gff.png" alt="" width="859" height="613" /></a></li>
</ol>
<h3>Deploying Firebase Hosting site</h3>
Deploying your site is very easy, just enter <code>firebase deploy</code> and within a few seconds the updated files are published. That's it! The same process is used for the initial deployment as well as all subsequent deployments.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/deploy.png"><img class="aligncenter size-full wp-image-2006" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/deploy.png" alt="" width="634" height="270" /></a>
<h3>Setting up a custom subdomain with Cloudflare</h3>
By default, your project will have a URL like <a href="https://fir-referenceproject.firebaseapp.com/">https://fir-referenceproject.firebaseapp.com/</a>. However, custom domains / subdomains can be added within a minute or two by following the simple steps displayed when "Connect domain" is pressed.
<h4>Define custom URL</h4>
First, enter your custom URL. I'll be using <a href="https://firebasehosting.jakelee.co.uk">https://firebasehosting.jakelee.co.uk</a>.
<h4>Verify domain ownership</h4>
Next, you need to prove you own the URL. This is done by adding a <code>TXT</code> record in your domain's DNS, accessible under the "DNS" tab in Cloudflare.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step2.png"><img class="aligncenter size-large wp-image-2007" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step2.png" alt="" width="730" height="414" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step2b.png"><img class="aligncenter size-large wp-image-2008" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step2b-1024x382.png" alt="" width="730" height="272" /></a>
<h4>Add <code>A</code> records</h4>
Finally, you now need to add the actual <code>A</code> records that will allow Firebase to serve content for your URL. This is done by adding 2 entries, each with your target subdomain as the "Name", and the specified IPs as the "IPv4 Address".

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step3.png"><img class="aligncenter size-large wp-image-2009" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step3.png" alt="" width="730" height="632" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step3b.png"><img class="aligncenter size-large wp-image-2010" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/step3b-1024x434.png" alt="" width="730" height="309" /></a>
<h2>Web interface</h2>
Firebase Hosting's very limited function set is easily seen when accessing the web interface. Each tab provides the bare essentials, and sometimes not even that!
<h3>Dashboard</h3>
The dashboard displays a list of all connected custom domains, and a list of all releases. Previous releases can be easily rolled back to or deleted, but no information is available about them. As such, an external source code manager like Git will still be essential for development.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard-2.png"><img class="aligncenter size-full wp-image-2011" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard-2.png" alt="" width="950" height="574" /></a>
<h3>Usage</h3>
The usage tab shows 2 simple graphs; your storage space used over time, and your data transfer size over time. The free "Spark" plan has a limit of 1GB of storage space, and 10GB/month of data transfer.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/usage.png"><img class="aligncenter size-full wp-image-2012" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/usage.png" alt="" width="960" height="540" /></a>
<h2>Conclusion</h2>
Before judging Firebase Hosting too harshly, it's important to remember that this isn't meant to replace traditional web hosts. Instead, it is intended as an extremely quick &amp; simple way to have a web app live on the internet, with CDN + HTTPS + custom domain, for free. Whilst it absolutely can be used for production-level apps, the lack of any controls may make even the most cutting-edge web developer a little bit hesitant.

Considering the amazingly easy to use deployment process, and the industry standard features offered for free, Firebase Hosting is definitely a contender for quick site prototypes. It can also hook into <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/">Firebase Cloud Functions</a> to handle server / database communication or data transfer with other systems.

I personally will most likely use it next time a simple idea needs testing, and eagerly await being up and running within a minute or two!

Previous: <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-storage">Developing Android Apps With Firebase Cloud Storage</a>