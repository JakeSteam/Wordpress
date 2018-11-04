---
ID: 1916
post_title: >
  How To Automatically Backup WordPress
  Posts To GitHub
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-automatically-backup-wordpress-posts-to-github/
published: true
post_date: 2018-11-04 19:00:50
---
When programming, most developers use GitHub (or another hosted git solution) to make sure all of their work is backed up in multiple places to ensure it is never lost. If you're anything like me, when writing blog posts you want the same peace of mind you have with the rest of your work. This post will cover how to automatically export all Wordpress posts to GitHub (and keep it updated), so that they can be imported later if necessary.

<!--more-->
<h2>Installing Wordpress / GitHub Sync</h2>
First, install the syncing plugin from the Wordpress.org directory. This can be done by going to the "Plugins" -&gt; "Add new" page, searching for "WordPress GitHub Sync", pressing "Install Now" and then "Activate". You can also download and upload it manually if you prefer from <a href="https://en-gb.wordpress.org/plugins/wp-github-sync/" target="_blank" rel="noopener">the plugin's page</a>. Note that the plugin is open source, and can be <a href="https://github.com/mAAdhaTTah/wordpress-github-sync" target="_blank" rel="noopener">viewed on GitHub</a>.
<h2>Configuring settings</h2>
A new option "GitHub Sync" will now appear under your Settings in the sidebar. Clicking this will take you to a somewhat complicated setup screen, here's what goes in each field:

<img class="alignnone size-full wp-image-1917" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/sync.png" alt="" width="914" height="713" />
<ol>
 	<li>GitHub hostname: This should not need to be changed, unless you're using a company-hosted GitHub.</li>
 	<li>Repository: The repository the exported posts will go into. This should be a public repository on your GitHub page, and should already have had the initialisation commit. GitHub <a href="https://help.github.com/articles/create-a-repo/" target="_blank" rel="noopener">provides a step by step guide</a> to creating a repository if you haven't done it before.</li>
 	<li>Oauth Token: This is needed to allow the plugin to perform actions (syncing) on your behalf. To create one, just visit your tokens page, fill in a description, tick <span style="font-family: 'courier new', courier, monospace;">public_repo</span>, and press "Generate token". You'll then be shown a secret 40-character string (with a green background), copy it into the plugin's form.</li>
 	<li>Webhook Secret: A webhook is needed for the repository to communicate back to the plugin. Create a new one for your GitHub repository by going to Settings -&gt; Webhooks. Fill in the form as follows:
<ol>
 	<li>Payload URL: Use the Webhook callback from step 6.</li>
 	<li>Content type: Change to "application/json".</li>
 	<li>Pick a secret password, this can be anything at all!</li>
 	<li>All other settings can be left at default settings.</li>
 	<li>Click "Add webhook".</li>
</ol>
</li>
 	<li>Default Import User: The user any posts imported <em>from</em> WordPress will post as. This defaults to your admin user.</li>
 	<li>Webhook callback: Already used for the Webhook secret.</li>
 	<li>Action buttons:
<ol>
 	<li>Export to GitHub: This will perform an initial backup of all posts, make sure to click it.</li>
 	<li>Import from GitHub: This would be used when restoring a backup, it isn't needed for this.</li>
</ol>
</li>
</ol>
<h2>Checking results</h2>
After pressing "Export to GitHub" in step 7.1 above, the plugin will begin working. After a few minutes (depending on the number of posts), you should see a repository filled up with all your posts and pages. The exported post contains a row of metadata (title, URL, author, publish date, etc), then the processed (HTML) version of your post, including all images and formatting. You can <a href="https://github.com/JakeSteam/Wordpress/blob/master/_posts/2018-11-01-how-to-migrate-a-subdomain-site-from-wordpress-com-to-another-host.md" target="_blank" rel="noopener">see an example exported post here</a>, from <a href="https://blog.jakelee.co.uk/how-to-migrate-a-subdomain-site-from-wordpress-com-to-another-host/">this post</a>.

Now, whenever you make a new post it will be automatically exported here. Nothing private has been exposed, as all of the posts are already publicly viewable, and the plugin's open-source nature (and limited permissions) means it's relatively trustworthy. I've been using it myself on this blog, and <a href="https://github.com/JakeSteam/Wordpress/tree/master/_posts" target="_blank" rel="noopener">it's successfully exported every post</a>. Awesome!

<img class="alignnone size-full wp-image-1919" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/sync2.png" alt="" width="993" height="601" />