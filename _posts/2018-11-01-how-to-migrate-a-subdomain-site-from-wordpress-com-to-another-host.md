---
ID: 1888
post_title: >
  How To Migrate A Subdomain Site From
  WordPress.com To Another Host
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-migrate-a-subdomain-site-from-wordpress-com-to-another-host/
published: true
post_date: 2018-11-01 21:50:41
---
<!-- wp:paragraph -->

As you may have (hopefully not!) noticed, this blog was recently moved from Wordpress.com's hosted solution to another host, namely <a href="https://www.bluehost.com/special/wordpress">Bluehost</a>. Bluehost were chosen due to being the only host recommended by both <a href="https://get.wp.com/hosting/">Wordpress.com</a> and <a href="https://wordpress.org/hosting/">Wordpress.org</a>, and their low prices. This post isn't sponsored by them!

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

This tutorial will walk through every step taken by myself during this process, to hopefully give you an idea of the work involved and avoid the same mistakes being made. Note that Wordpress.com also offer a "<a href="https://en.support.wordpress.com/guided-transfer/">guided transfer</a>" for $129, but none of the steps are too challenging if you've got a few hours spare! Before beginning this tutorial, it's worth pointing out that I used Cloudflare to handle my site's subdomain being provided by a different host. If you are using a raw domain (e.g. http://example.com), or another CDN / domain registrar, the Cloudflare steps will be slightly different. The other steps will be unchanged.

<!-- /wp:paragraph -->

<!-- wp:more -->
<!--more--><!-- /wp:more -->

<!-- wp:heading -->
<h2>Preparing old blog</h2>
<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3 id="mce_7">Disable scheduled / automatic actions</h3>
<!-- /wp:heading -->

<!-- wp:paragraph -->

The first step in transferring host is preparing your blog for the migration, so that nothing interferes with the transfer.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

If there are any scheduled posts, their scheduled times should be changed to a day or so later, otherwise they may get published during the transfer and not be published correctly.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

Next, any automatic actions hooked into your Wordpress.com account need to be temporarily disabled, as they may get confused. For example, I was using <a href="https://ifttt.com">IFTTT</a> to <a href="https://ifttt.com/connect/wordpress/twitter">automatically tweet blog posts</a> on my Twitter (<a href="https://twitter.com/JakeLeeLtd/status/1052271107330465792">example</a>). I disabled this recipe during the transfer to avoid all old posts being seen on the new blog for the first time and being tweeted. If you have plugins installed (not available on most Wordpress.com plans), make sure any that may interfere are disabled.

<!-- /wp:paragraph -->

<!-- wp:paragraph -->

<!-- /wp:paragraph -->

<!-- wp:heading {"level":3} -->
<h3>Export data</h3>
To export all of your existing pages, posts, categories, tags, and much more, Wordpress luckily includes a built in exporter. This can be found at <span style="font-family: 'courier new', courier, monospace;">yoursite.com/wp-admin/export.php</span>, make sure "All content" is selected for export. Clicking "Download Export File" will then queue up an export, a link to which will be emailed to you within a few minutes.

<img class="alignnone size-full wp-image-1905" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/wordpress.png" alt="" width="813" height="401" />

This export will consist of an XML file (technically a <strong>W</strong>ordpress E<strong>x</strong>tended <strong>R</strong>SS file), which will be needed in the next step.

<!-- /wp:heading -->

<!-- wp:heading -->
<h2>Set up new blog</h2>
Once you've selected your plan, paid, and filled in all necessary information, you should have access to your new wordpress dashboard (e.g. <a href="https://my.bluehost.com/cgi/app#/sites" target="_blank" rel="noopener">Bluehost's</a>). Currently your brand new blog will have an example post, and a default theme. It'll also be hosted on an odd domain similar to <span style="font-family: 'courier new', courier, monospace;">box1234.temp.domains/~yourusername</span>. Your old blog will continue to work until you move onto the next step, Cloudflare, so take your time ensuring your new blog design is perfect before continuing.

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Import data</h3>
To import data, first install and activate the <a href="https://wordpress.org/plugins/wordpress-importer/" target="_blank" rel="noopener">Wordpress Importer</a> plugin, it may even be enabled by default. Once this is done, under the "Import" page under "Tools", the wordpress option will have a "Run importer" link, taking you to the plugin's import page. Upload your export file from your old blog, and import it. All posts, pages, categories, and tags should now be imported successfully, although you may notice some minor formatting issues. Many of these will be fixed in the following steps, so it's recommended to fix any incorrectly formatted posts after all of the following steps have been completed.

<img class="alignnone size-full wp-image-1910" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/wordpress-1.png" alt="" width="900" height="225" />

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Repick theme</h3>
Assuming you were using a built-in Wordpress.com theme, it should be available in your host's theme library. If not, it should be available in the official Wordpress.org theme library. These can be previewed and installed very easily via the "Themes" page under "Appearance".

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Reconfigure theme</h3>
You may have configured your theme to have custom widgets in your sidebar(s). Make sure to also add these all to your new blog, you can use your old blog as a reference to ensure none are missed / misconfigured.

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Reinstall plugins</h3>
Finally, go through your plugins list and make sure they have all been installed again, copying across any configurations as necessary. This may also be a good time to trim your plugins list (<span style="font-family: 'courier new', courier, monospace;">/wp-admin/plugins.php</span>), and get rid of any you don't actually need!

<!-- /wp:heading -->

<!-- wp:heading -->
<h2>Set up Cloudflare / Domain Host</h2>
Cloudflare is going to be used to redirect all requests to your blog's subdomain (e.g. blog.example.com) to your host. This can be a little tricky, as there were a few missteps during my attempt (at least when transferring to Bluehost).

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Get account IP</h3>
On your host (e.g. Bluehost), find out which IP your blog is served from. For Bluehost, this can be found under "Advanced" -&gt; "Server Information" -&gt; "Shared IP Address". You can also open command prompt and enter  <span style="font-family: 'courier new', courier, monospace;">ping temporaryblogurl.yourhost.com</span> (using your temporary blog URL of course!) and it should return  the IP it is being served from. I didn't realise this when setting it up, and had to get their (very patient) chat support to help me find it!

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Set up CNAME + A record</h3>
Now that you have the IP, you can tell Cloudflare (or wherever else your site is hosted) to redirect all blog subdomain requests to it. This is done by adding (or editing) 2 new "A" (alias) records. The first should have a Name of "blog" and an IPv4 address of your IP from earlier. The second should have a Name of "www.blog" and the same IPv4 address again. The "Status" should also be set to DNS only (grey cloud), or you'll experience redirect issues caused by your host struggling to direct traffic properly.

These changes should take effect immediately, but can take a few hours to propagate around various servers on the internet and be properly applied.

<img class="alignnone size-full wp-image-1911" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/cloudflare.png" alt="" width="1035" height="397" />

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Change site URL on BlueHost / your host</h3>
Now, your new site needs to know where you just set it up to appear! It needs to know the URL it is appearing at, this ensures all page links go to the correct place and no images / other content is lost.

On Bluehost, this option is found under "My Sites", mouseover your new site then "Manage Site". Under the "Settings" tab the Site URL can be set to the full URL of your blog. You should also use this opportunity to look at the other settings on this page, and turn off the "Coming Soon Page" to make sure users can actually see your site.

<img class="alignnone size-full wp-image-1912" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/bluehost.png" alt="" width="1206" height="463" />

<!-- /wp:heading -->

<!-- wp:heading {"level":3} -->
<h3>Fix URLs</h3>
You may notice that your post's URLs have changed. My post structure changed from <span style="font-family: 'courier new', courier, monospace;">domain.com/year/month/day/post-name</span> to <span style="font-family: 'courier new', courier, monospace;">domain.com/post-name</span>. I decided I preferred this change, so updated any internal links to point to the correct URL.  I also had <a href="https://search.google.com/search-console/about" target="_blank" rel="noopener">Google Search Console</a> enabled, so I was informed of any broken links. To ensure any search results still worked, I used <a href="https://wordpress.org/plugins/wordpress-seo/" target="_blank" rel="noopener">Yoast SEO</a>'s "Tools" -&gt; "File editor" to redirect all of the old format links to the new format. I did this by adding the following line to the top of my <span style="font-family: 'courier new', courier, monospace;">.htaccess</span> file:
<pre>RewriteRule ^([0-9]+)/([0-9]+)/([0-9]+)/(.*)$ /$4 [R=301,NC,L]</pre>
There are other tools for editing this file, I just used Yoast because I already had it installed.
<h3>Fix posts</h3>
Finally, have a look over your posts, particularly the most complicated ones. Whilst most of the earlier issues should be resolved by the newly installed plugins and fixed URL, minor issues may remain. For example, on my blog the code formatting had broken slightly due to the plugin used, so some of my posts had to be corrected manually.
<h2>Conclusion</h2>
Whilst moving away from the simple and straightforward Wordpress.com can be intimidating, the freedom provided by other hosts is easily worth it. Most hosts offer plans similarly priced to the most basic Wordpress.com plan, but offer the ability to customise your site as much as you like. The ability to install plugins also means you can set up functionality (for free) like <a href="https://wordpress.org/plugins/social-networks-auto-poster-facebook-twitter-g/" target="_blank" rel="noopener">autoposting to social media</a>, <a href="https://wordpress.org/plugins/wordpress-seo/" target="_blank" rel="noopener">improving your SEO</a>, and <a href="https://en-gb.wordpress.org/plugins/wp-github-sync/" target="_blank" rel="noopener">backing up your site's posts to another site</a>.

<!-- /wp:heading -->