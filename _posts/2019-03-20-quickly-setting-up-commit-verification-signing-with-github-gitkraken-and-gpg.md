---
ID: 2436
post_title: >
  Quickly setting up commit verification /
  signing with GitHub, GitKraken, and GPG
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/quickly-setting-up-commit-verification-signing-with-github-gitkraken-and-gpg/
published: true
post_date: 2019-03-20 21:31:04
---
Whilst most developers use hosted git repositories on a service like GitHub, many forget that almost none of these commits are verified. If you own a repository, you can <a href="https://dev.to/agrinman/spoof-a-commit-on-github-from-anyone-4gf4" target="_blank" rel="noopener noreferrer">"fake" a commit</a> from literally any user if you know their email. If that email matches a GitHub account, their avatar will be displayed next to their name! One famous example is a fake commit by Linus Torvalds:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/VFQqkEo.png"><img class="aligncenter size-medium wp-image-2437" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/VFQqkEo-300x110.png" alt="" width="300" height="110" /></a>

An effortless way to protect against this is with git verified signatures. This proves that a commit was <em>really</em> from the person. GitKraken <a href="https://support.gitkraken.com/release-notes/current/#version-500" target="_blank" rel="noopener noreferrer">introduced this feature a week ago</a>, and it seems to work perfectly. This tutorial will provide a very simple guide to getting verified commits configured.

<!--more-->

Note that GitKraken also has <a href="https://support.gitkraken.com/git-workflows-and-extensions/commit-signing-with-gpg/" target="_blank" rel="noopener noreferrer">a very in-depth guide</a> with lots of extra information.
<h2>Installing GPG</h2>
First, <a href="https://gpg4win.org/get-gpg4win.html" target="_blank" rel="noopener noreferrer">download Gpg4win</a> (select $0 donation if you do not wish to donate, <a href="https://support.gitkraken.com/git-workflows-and-extensions/commit-signing-with-gpg/#commit-signing-requirements" target="_blank" rel="noopener noreferrer">mac / linux options also available</a>).

Next follow the installer's steps, deselecting GPGOL (Outlook email signing) and GPGEX (Right-click signing).

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/2HFJNf1.png"><img class="aligncenter size-full wp-image-2447" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/2HFJNf1.png" alt="" width="499" height="388" /></a>

GPG is now installed!
<h2>Getting a GPG key</h2>
Under GitKraken's GPG Preferences (File -&gt; Preferences -&gt; GPG Preferences), browse for your newly installed GPG program. By default, this is at <code>C:\Program Files (x86)\GnuPG\bin\gpg.exe</code>.

Now that GitKraken knows about GPG, you can press "Generate", with an optional passphrase.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/Qrw6Wbp.png"><img class="aligncenter wp-image-2438 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/Qrw6Wbp.png" alt="" width="551" height="274" /></a>

After a few seconds, you will now have a GPG signing key! The "Signing Key" field of GitKraken's GPG Preferences screen is now populated with your new key.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/hzQUOhK.png"><img class="aligncenter size-medium wp-image-2439" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/hzQUOhK-300x90.png" alt="" width="300" height="90" /></a>

Make sure to tick both the "Sign Commits by Default" and "Sign Tags by Default" checkboxes, so all future actions are signed. You should end up with a preferences screen like this:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/ovIoBca.png"><img class="aligncenter size-full wp-image-2441" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/ovIoBca.png" alt="" width="681" height="401" /></a>

Your GitKraken is now configured to use commit verification! Time to sort out GitHub...
<h2>Adding your GPG key to GitHub</h2>
<ol>
 	<li>Click "Copy GPG Public Key" in the GitKraken GPG Preferences screen. This will copy your public key to your clipboard, ready to give to GitHub.</li>
 	<li>Go to <a href="https://github.com/settings/gpg/new" target="_blank" rel="noopener noreferrer">GitHub's "Add new GPG key" screen</a>.</li>
 	<li>Paste in your public key from step 1, and press "Add GPG key".</li>
 	<li>You may need to reconfirm your password, then it's been added!<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/KKkNvmr.png"><img class="aligncenter size-full wp-image-2445" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/KKkNvmr.png" alt="" width="762" height="253" /></a></li>
</ol>
<h2>Testing your new signed commits</h2>
Try making a commit, you should now see a green icon next to your commit hash in GitKraken. You can mouseover it for more information about your signed commit:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/NsMyHsN.png"><img class="aligncenter size-full wp-image-2443" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/NsMyHsN.png" alt="" width="594" height="266" /></a>

When you push this commit, GitHub will also reflect your verified commit status:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/EzwjkUn.png"><img class="aligncenter size-full wp-image-2444" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/03/EzwjkUn.png" alt="" width="961" height="322" /></a>

Your future commits are now all verified!

Considering how popular <a href="https://www.gitkraken.com/download" target="_blank" rel="noopener noreferrer">GitKraken</a> is becoming (it's my personal client of choice), being able to implement commit signing so easily provides yet another reason to switchover. Going forward, I fully intend to verify all my commits, with no extra effort beyond the original setup!