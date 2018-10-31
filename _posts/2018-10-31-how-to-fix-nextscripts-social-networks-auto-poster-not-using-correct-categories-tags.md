---
ID: 1901
post_title: 'How To Fix Nextscripts&#8217; Social Networks Auto-Poster Not Using Correct Categories / Tags'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-fix-nextscripts-social-networks-auto-poster-not-using-correct-categories-tags/
published: true
post_date: 2018-10-31 20:11:26
---
This blog uses <a href="https://wordpress.org/plugins/social-networks-auto-poster-facebook-twitter-g/" target="_blank" rel="noopener">Nextscript's SNAP</a> to autopost new posts on LinkedIn, Twitter, and Medium. After installing and activating a few new plugins, I noticed that a scheduled post ("<a href="https://blog.jakelee.co.uk/how-to-view-trello-card-name-description-history/">How To View Trello Card Name / Description History</a>") had been posted with the default category (Android Dev) instead of the correct category (Project Management). Additionally, none of the post's tags were included in any of the automatic posts.

<a href="https://twitter.com/JakeLeeLtd/status/1057354288425525250"><img class="aligncenter wp-image-1903 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/10/twitter.png" alt="" width="592" height="165" /></a>

After a little trial and error, I discovered the cause: <a href="https://wordpress.org/gutenberg/">Gutenberg Editor</a>. Creating any posts in this new editor caused the issue, and just disabling the plugin resolved the issue instantly.

Whilst the plugin was useful, and had a lot of improvements over the default TinyMCE editor, it wasn't worth ruining all social media posts!