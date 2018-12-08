---
ID: 2134
post_title: >
  How To Free Up Space in Google Drive,
  Gmail, and Google Photos
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-free-up-space-in-google-drive-gmail-and-google-photos/
published: true
post_date: 2018-12-08 15:21:26
---
I've been lightly using Google Drive, Gmail, and Google Photos for close to 6 years, with no major complaints. The default space limitation is 15GB, with an extra 2GB available for each year the <a href="https://blog.google/products/drive/safer-internet-day-2016/" target="_blank" rel="noopener">security checkup is completed</a>. There was also previously 100GB (expiring after a year) available for local guides, but this <a href="https://www.androidpolice.com/2017/03/03/new-level-4-local-guides-google-maps-will-no-longer-receive-free-100gb-drive-storage/">program is now cancelled</a>.

Recently I realised I was getting unnervingly close (84%) to my 19GB limit, and went on a quest to minimise this amount as much as possible, eventually getting it down to 6% without losing any useful content!<!--more-->

First, <a href="https://one.google.com/storage" target="_blank" rel="noopener">view your overall usage at Google One</a>, this will help you guide your efforts to the biggest space hoggers. My initial overview is shown below, further updates will be shown throughout this tutorial.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage1.png"><img class="aligncenter size-full wp-image-2142" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage1.png" alt="" width="654" height="446" /></a>
<h2>Drive</h2>
The biggest user of space was Google Drive, so that seemed a sensible place to start. A three pronged attack would be used; deleting large files, deleting unused files, and emptying the bin.
<h3>Emptying Google Drive bin</h3>
I have <a href="https://www.google.com/drive/download/" target="_blank" rel="noopener">Google Drive synced to a local folder on my PC</a>, so my first step was checking the disk space used. <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagelocal.png"><img class="alignright size-medium wp-image-2143" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagelocal-226x300.png" alt="" width="226" height="300" /></a>It turned out to be just 1GB, so where on earth was the extra 8.4GB coming from!?

After a little research, I discovered every deleted file in Google Drive is kept... forever. That 500MB video you use Google Drive to transfer 5 years ago, then deleted the next day? It's still there! Those few thousand image assets you downloaded for a project then ended up deleting? Still there!

Luckily, it is possible to empty this bin. Just <a href="https://drive.google.com/drive/u/0/trash" target="_blank" rel="noopener">visit the Trash</a>, then click "Bin" and "Empty bin". Due to the 6 years of files hidden away in there for me, this process took around half an hour to complete. During that time, the Trash would show a random selection of files, so it was hard to tell if it was actually making any progress. Viewing the <a href="https://one.google.com/storage" target="_blank" rel="noopener">storage overview page</a> gives a more up to date account of your space usage. Note that due to the time taken to empty the bin, later screenshots in this tutorial will have ever-decreasing Google Drive sizes!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/emptybin.png"><img class="aligncenter wp-image-2146 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/emptybin.png" alt="" width="347" height="119" /></a>
<h3>Cleaning out Google Drive</h3>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/quota.png"><img class="alignright size-medium wp-image-2160" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/quota-300x167.png" alt="" width="300" height="167" /></a>First, visit <a href="https://drive.google.com/drive/u/0/quota" target="_blank" rel="noopener">the quota page</a> and sort by "Storage used". This will show you your largest files, and let you delete them. This is useful for any forgotten large files (e.g. videos, 3D renders) that are no longer useful.

However, this approach misses any small files, and fails to give you a more general overview of your space used. Luckily, <a href="https://windirstat.net/index.html" target="_blank" rel="noopener">WinDirStat (Windows Directory Statistics)</a> exists, an application which provides a very detailed analysis of the space used in a specific folder.

Whilst WinDirStat definitely isn't a pretty program, or particularly easy to understand the first time round, it's worth learning. When run on your Google Drive folder, it will tell you:
<ul>
 	<li>Which folders / subfolders are using the most space (top left)</li>
 	<li>Which file types are using the most space (top right)</li>
 	<li>A visual representation of every file, with larger ones appearing larger.</li>
</ul>
Clicking on a file's visual representation will show you the location, filename, and filetype. Using this information, you can delete any large files / collections of files you no longer wish to keep. For example, the screenshot below has a large <code>.psd</code> selected.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/windirstat.png"><img class="aligncenter size-medium wp-image-2161" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/windirstat-300x190.png" alt="" width="300" height="190" /></a>

Now that large / unneeded files have been removed and the bin has been emptied, the Google Drive space used is just 30% of the original, a very nice start!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage3.png"><img class="size-medium wp-image-2147 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage3-300x215.png" alt="" width="300" height="215" /></a>
<h2>Gmail</h2>
As I use Gmail for all of my daily emails (across at least 5 accounts), there are tens of thousands of emails that have been archived forever and forgotten about. Whilst Gmail only uses 1.35GB, that seemed pretty excessive considering I'm not intentionally keeping any files / emails!
<h3>Large files</h3>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagemore.png"><img class="alignright size-medium wp-image-2148" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagemore-300x245.png" alt="" width="300" height="245" /></a>The first task is removing any large attachments that are hidden away in old emails. Luckily, <a href="https://support.google.com/mail/answer/7190" target="_blank" rel="noopener">Gmail has extremely powerful search operators</a> that can be used to filter emails. For this task we want to delete emails with attachments, that are at least 20MB in size. To do this, enter the following into the Gmail search bar:
<pre>has:attachment size:20MB</pre>
You can of course change <code>20MB</code> to <code>10MB</code> or any other amount, depending on how aggressively you want to free up space.
<h3>Junk emails</h3>
Next, I noticed that there were thousands and thousands of emails that had already been actioned, or were just notifications in email form. Whilst there were around 4,000 unread emails like this, there were at least 25,000 already read!

Luckily, Gmail automatically categorises most emails into "Social", "Updated", "Forums", or "Promotions". Clearing these out was just a case of:
<ol>
 	<li>Going to each folder</li>
 	<li>Clicking the "Select all" square at the top</li>
 	<li>Clicking the "Select all X messages in Y" bar at the top</li>
 	<li>Pressing the trash icon</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagecategories.png"><img class="size-full wp-image-2149 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagecategories.png" alt="" width="276" height="160" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagefolders.png"><img class="size-medium wp-image-2150 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagefolders-300x99.png" alt="" width="300" height="99" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagefolders2.png"><img class="size-medium wp-image-2151 alignnone" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storagefolders2-300x133.png" alt="" width="300" height="133" /></a>

&nbsp;

Deleting thousands of emails can take a few seconds, make sure not to close the tab before it is done! The space used by Gmail has now dropped from 1.35GB to 0.41GB, without anything important being lost. Note that Google Drive's space is still slowly going down, as it works through the thousands of deleted files from the previous step!<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage6.png"><img class="aligncenter size-medium wp-image-2152" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage6-300x209.png" alt="" width="300" height="209" /></a>

&nbsp;
<h2>Google Photos</h2>
The final section to clear out is Google Photos. For this, I used Google Photos' unlimited "High Quality" storage option. With this option, your full quality photos are replaced with "high" quality ones, which don't count towards your allocation. High quality is defined as a maximum of 16MP, which is good enough for everyday usage (especially with my poor photography skills!).<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/compress1.png"><img class="size-medium wp-image-2153 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/compress1-300x261.png" alt="" width="300" height="261" /></a>

<img class="size-medium wp-image-2154 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/compress2-300x124.png" alt="" width="300" height="124" />

To apply this setting, visit your <a href="https://photos.google.com/settings" target="_blank" rel="noopener">Google Photos settings</a> and change the "Upload size" setting from "Original" to "High quality". This process takes around 5 minutes per 500MB, and is a good way of freeing up a large amount of space for very little effort.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage7.png"><img class="aligncenter size-medium wp-image-2156" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage7-300x204.png" alt="" width="300" height="204" /></a>The space used by Google Photos has now dropped from 5.32GB to 0GB. Pretty good!
<h2>Conclusion</h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage1-1.png"><img class="size-medium wp-image-2155 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage1-1-300x205.png" alt="" width="300" height="205" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage7.png"><img class="size-medium wp-image-2156 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/storage7-300x204.png" alt="" width="300" height="204" /></a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

So, the total space used has dropped from 16.07GB to 1.14GB, and from 84% utilisation to just 6%. Plenty of space now!

Emptying the Google Drive trash was by far the biggest space saving action, freeing up around 8GB of space. Whilst I have relied on Google Drive's trash in the past, it's surprising that it can grow to be 8x the size of the actual files, and not have any automatic emptying. Gmail's trash folder is emptied after 30 days naturally.

If these space freeing techniques don't free up enough space, there <strong>are</strong> paid plans available. The prices are very reasonable, with plans ranging from 100GB (£1.59/mo) to 30TB (£239.99/mo). Dropbox only offers a <a href="https://www.dropbox.com/upgrade" target="_blank" rel="noopener">single 2TB paid plan</a>, whilst <a href="https://onedrive.live.com/about/en-GB/plans/" target="_blank" rel="noopener">OneDrive offer 3 premium plans</a>, all more expensive than comparable Google One plans. As well as having very competitive pricing, the main advantages of Google's plans are their integration into services you're likely using already (Google Photos, Gmail, Google Drive).