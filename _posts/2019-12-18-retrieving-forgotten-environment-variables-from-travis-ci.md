---
ID: 2615
post_title: >
  Retrieving forgotten environment
  variables from Travis CI
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/retrieving-forgotten-environment-variables-from-travis-ci/
published: true
post_date: 2019-12-18 15:00:37
---
When using a CI server, you'll often need a way to use highly sensitive strings (e.g. signing keys, API keys, passwords), whilst minimising who has access to them. Travis CI solves this using <a href="https://docs.travis-ci.com/user/environment-variables/#Encrypted-Variables" target="_blank" rel="noopener noreferrer">encrypted environment variables</a>, encrypting your variables using a private key only Travis has access to. These encrypted values are then stored in your <code>.travis.yml</code>.

This means nobody sees your plaintext string except Travis, and it is never sent between servers. The downside of this? <strong>If you lose the secret string, only Travis knows what it is!</strong>

When transitioning from APKs to app bundles recently (article soon!), I discovered our keystore's password wasn't stored anywhere besides Travis. The entire app being dependent on our CI staying up scared me! As such, I decided to extract the plaintext value to store in the same secure area as other company passwords.

<!--more-->

Since Travis (correctly) tries very hard to stop you getting these variables back out, you'll need to take a roundabout approach. I also didn't want our password ever being displayed in the logs, or sent over the internet in plaintext.
<h2>The plan</h2>
<ol>
 	<li>Create a new secure variable as an encryption key.</li>
 	<li>During a build decrypt our target variable into a file, then encrypt the file using our new encryption key.</li>
 	<li>Transfer this encrypted file off the CI server using <a href="https://file.io" target="_blank" rel="noopener noreferrer">file.io</a>.</li>
 	<li>Download it, then decrypt using the encryption key we created in step 1.</li>
</ol>
<h2>Preparing Travis CI</h2>
Before running the script, you will need to add a new environment variable to Travis called <code>ENC_KEY</code>, and make it only available on your current branch. It should end up looking like this:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/12/Screenshot-2019-12-16-at-08.41.10.png"><img class="alignleft size-full wp-image-2616" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/12/Screenshot-2019-12-16-at-08.41.10.png" alt="" width="1642" height="326" /></a>

Note: It's good practice to always limit the branches your environment variables can run on.
<h2>Preparing the build config</h2>
Next, add the following to your <code>.travis.yml</code> build config. Don't forget to replace:
<ul>
 	<li><code>MY_VARIABLE</code> (two times) with your target encrypted variable's name.</li>
 	<li><code>arandomfilename</code> (three times) with a string of your choice.</li>
</ul>
<pre id="rccb_itvplayer_android:.travis.yml@5cbc3f3144306ca4" class="code-block language-yaml ember-view codedisplay line-numbers"><code class="language-yaml"><span class="token key atrule">install</span><span class="token punctuation">:</span>
  <span class="token punctuation">-</span> sudo apt<span class="token punctuation">-</span>get install <span class="token punctuation">-</span>y ccrypt
  <span class="token punctuation">-</span> echo MY_VARIABLE = $MY_VARIABLE <span class="token punctuation">&gt;</span> arandomfilename.txt
  <span class="token punctuation">-</span> ccencrypt arandonfilename.txt <span class="token punctuation">-</span>K $ENC_KEY
  <span class="token punctuation">-</span> curl <span class="token punctuation">-</span>F'file=@arandomfilename.txt.cpt' https<span class="token punctuation">:</span>//file.io</code></pre>
This script installs ccrypt, puts our decrypted variable into a file, encrypts the file, and hosts it.
<h2>Running the script</h2>
<ol>
 	<li>Commit your build config changes.</li>
 	<li>Push the branch.</li>
 	<li>You should now see a response with a URL after your last cURL statement, e.g:</li>
</ol>
<pre class="log-line"><span id="0-1324">$ curl -F'file=@arandomfilename.txt.cpt' https://file.io</span>
{"success":true,"key":"he5h9","link":"https://file.io/he5h9","expiry":"14 days"}</pre>
Note: This line of the log may be hidden by default by Travis, make sure to click any "expand" arrows.
<h2>Retrieving the value</h2>
<ol>
 	<li>First, you need to download your encrypted file from the returned URL using curl (e.g. <code>curl -O https://file.io/he5h9</code>) or by opening it in a browser.</li>
 	<li>Next, you need ccrypt installed to decrypt it (e.g. <code>brew install ccrypt</code>).</li>
 	<li>Finally, decrypt it (e.g. <code>ccrypt -d arandomfile.cpt</code>).</li>
 	<li>You now have a text file containing your target value!</li>
</ol>
<h2>Additional notes</h2>
Whilst the ability to retrieve a supposedly secure variable may seem concerning, it's important to note this is only possible if you have push access to the repo. Travis also has the ability to only allow variables to be accessed from specific branches (e.g. <code>master</code>), which should be used when possible.

Also, whilst transferring even an encrypted value off-site may seem scary, file.io has extra protection. Once your file has been downloaded once, it can never be downloaded again. Thus, there's no risk of someone else randomly stumbling across the file.

Finally, file hosts tend to come and go pretty quickly. If <a href="https://file.io" target="_blank" rel="noopener noreferrer">file.io</a> no longer exists, any other command line based upload service will work!

<em>This article is based on <a href="https://www.topcoder.com/recover-lost-travisci-variables-two-ways/" target="_blank" rel="noopener noreferrer">an excellent (but outdated) article by Matthew Twomey</a>, which also covers how to avoid needing a third party host at all.</em>