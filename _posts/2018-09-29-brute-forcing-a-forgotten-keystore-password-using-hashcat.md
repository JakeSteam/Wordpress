---
ID: 1753
post_title: >
  Brute Forcing A Forgotten Keystore
  Password Using Hashcat
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/brute-forcing-a-forgotten-keystore-password-using-hashcat/
published: true
post_date: 2018-09-29 14:00:20
---
Recently, I was preparing an update to a long abandoned Android app of mine when I realised the password to the keystore was long forgotten. A keystore and associated password is essential for updating an app (more information on keystores is available in <a href="https://www.youtube.com/watch?v=3lDtAf8Jk_c" target="_blank">easy to understand LEGO form</a>), and as such the app could never be updated again!

Luckily, it's possible to crack the password to a keystore.

<!--more-->

For this tutorial, the debug keystore will be used, but the steps are exactly the same for a release keystore, i.e. one used to update an app in the store. The debug keystore is located at <code>C:\Users\yourusername\.android\debug.keystore</code>.

<h2>Retrieving hash</h2>

To retrieve the keystore's hash so it can be cracked, we are going to use a useful little 10KB utility called <code>JksPrivkPrepare.jar</code>.

<ol>
<li>First, <a href="https://github.com/floyd-fuh/JKS-private-key-cracker-hashcat/archive/master.zip" target="_blank">download floyd-fuh's hash retriever</a> (<a href="https://github.com/floyd-fuh/JKS-private-key-cracker-hashcat" target="_blank">source code</a>).</li>
<li>Unzip the <code>JKS-private-key-cracker-hashcat-master.zip</code> archive just downloaded.</li>
<li>Paste your target keystore inside the unzipped folder (in the same folder as the <code>README.md</code> etc).</li>
<li>Open up a command prompt / PowerShell prompt. Pressing shift + right click within the unzipped folder should provide an option to "Open Command Prompt here" or "Open PowerShell window here".</li>
<li>Paste (right click -&gt; paste) the following, replacing <code>debug.keystore</code> with your keystore's name: <code>java -jar JksPrivkPrepare.jar debug.keystore &gt; hash.txt</code>, then press enter.</li>
<li>A <code>hash.txt</code> should now exist in the folder, that's the hash we need! The tool will also tell you your key's alias, shown below:</li>
</ol>

<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/leovqan.png" alt="leovqan" width="884" height="172" class="alignnone size-full wp-image-1754" />

<h2>Preparing the hash</h2>

On Windows machines, <code>hash.txt</code> is output in a slightly incorrect format (contains a <a href="https://www.w3.org/International/questions/qa-byte-order-mark" target="_blank">BOM</a>, which files on Windows shouldn't have). The easiest solution is to open <code>hash.txt</code> in <a href="https://notepad-plus-plus.org/download/v7.5.8.html" target="_blank">Notepad++</a>, convert it, then resave it.
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/d22ooef.png" alt="d22ooef" width="357" height="312" class="alignnone size-full wp-image-1755" />

<h2>Cracking the hash</h2>

<ol>
<li>First, <a href="https://hashcat.net/hashcat/" target="_blank">download Hashcat</a> by clicking "Download" on the "hashcat binaries" row.</li>
<li>Unzip the archive (this may require <a href="https://www.7-zip.org/download.html" target="_blank">7zip</a>).</li>
<li>Move the fixed hash.txt from earlier into the unzipped folder.</li>
<li>Run <code>.\hashcat64 -m 15500 -a 3 -1 '?l' -w 3 hash.txt ?1?1?1?1?1?1?1</code>. (<code>hashcat32</code> on 32-bit systems, more detail on this command in the next section)</li>
<li>After a few seconds, you should see the very long hash we retrieved earlier followed by <code>:android</code>, telling us that the cracked password is "android"!</li>
</ol>

<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/lpp8e6k.png" alt="lpp8e6k" width="1454" height="864" class="alignnone size-full wp-image-1756" />

<h2>Further crack configuration</h2>

The command entered earlier, <code>.\hashcat64 -m 15500 -a 3 -1 '?l' -w 3 hash.txt ?1?1?1?1?1?1?</code> is pretty overwhelming at first glance, but each section can be understood individually. A <a href="https://hashcat.net/wiki/doku.php?id=hashcat" target="_blank">full list of Hashcat parameters is available</a>, the following settings are sufficient for this purpose however. All reference tables below come from the official documentation.

<ul>
<li><b>.\hashcat64</b>: Tells Windows we're trying to use <code>hashcat64.exe</code>. On 32-bit machines this should be <code>hashcat32.exe</code>.</li>
<li><b>-m 15500</b>: Sets the hash type to "JKS Java Key Store Private Keys (SHA1)", so that hashes can be compared.</li>
<li><b>-a 3</b>: Sets the attack mode to "Brute-force", e.g. trying every possible password until the correct one is found.</li>
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/p5utt0q.png" alt="p5utt0q" width="223" height="144" class="alignnone size-full wp-image-1757" />
<li><b>-1 '?l'</b>: Sets the first character set to <code>l</code> for lowercase. A password with uppercase + lowercase letters as well as numbers would need <code>?u?l?d</code>.</li>
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/3tndajn.png" alt="3tndajn" width="286" height="192" class="alignnone size-full wp-image-1758" />
<li><b>-w 3</b>: Sets the workload profile (intensity) to "High".</li>
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/inwz4ub.png" alt="inwz4ub" width="472" height="134" class="alignnone size-full wp-image-1759" />
<li><b>hash.txt</b>: Defines the list of hashes to crack, our file only has one.</li>
<li><b>?1?1?1?1?1?1?</b>: This sets the "mask" used for the search. Each instance of <code>?1</code> refers to the character set we defined earlier, and says there is a character from that character set in that position. We repeat this 7 times since it's a 7 letter password, usually you would try 5 characters, then 6, etc.</li>
</ul>

The ability to set multiple character sets in the mask allows for situations where you know the password is a letter followed by numbers, or another pattern.

<h2>Conclusion</h2>

The bulk of the work in this post is done by <a href="https://hashcat.net/hashcat/" target="_blank">Hashcat</a>, an extremely powerful hash cracking tool that has been around for years and is used by everyone from penetration testers to malicious hackers. That being said, it is simply a useful tool, not at all illegal!

Password cracking can be extremely complicated, but due to Hashcat's popularity there are thousands of guides online. Alternatively, <a href="https://hashcat.net/forum/" target="_blank">the official forums</a> can generally help out.

PS: Obviously, I don't condone cracking any keystores that aren't your own!