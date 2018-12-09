---
ID: 2173
post_title: >
  Creating a history clearing Chrome
  extension
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-history-clearing-chrome-extension/
published: true
post_date: 2018-12-09 18:21:35
---
Chrome extensions are small plugins that modify how Chrome (or websites viewed in it) look and behave. These can notify you of events (e.g. <a href="https://chrome.google.com/webstore/detail/google-mail-checker/mihcahmgecmbnbcchbopgniflfhgnkff">Google Mail Checker</a> or <a href="https://chrome.google.com/webstore/detail/inoreader-companion/kfimphpokifbjgmjflanmfeppcjimgah">Inoreader Companion</a>), modify sites (e.g. <a href="https://chrome.google.com/webstore/detail/reddit-enhancement-suite/kbmfpngjjgdllneeigpgjifpgocmfgmb">Reddit Enhancement Suite</a> or <a href="https://chrome.google.com/webstore/detail/ublock-origin/cjpalhdlnbpafiamejdnhcphjbkeiagm">uBlock Origin</a>), or add functionality (e.g. <a href="https://chrome.google.com/webstore/detail/https-everywhere/gcbommkclmclpchllfjekcdonpmejbdp">HTTPS Everywhere</a>).

A couple of years ago, I made a very simple Chrome extension called <a href="https://chrome.google.com/webstore/detail/selective-scrubber/ljjdcbfpdmjelppcfeiadnjagdhompkb">Selective Scrubber</a> that removed predefined domains &amp; terms from your Chrome history. This tutorial will cover the code behind it, as well as how to upload an extension like this.

<!--more-->

Before diving into this project, I strongly recommend <a href="https://chrome.google.com/webstore/detail/selective-scrubber/ljjdcbfpdmjelppcfeiadnjagdhompkb">looking at the final result</a>, and checking out the <a href="https://github.com/JakeSteam/SelectiveScrubber">extension's repository</a>. It's also worth noting that this extension doesn't work if using Chrome history sync, due to <a href="https://github.com/JakeSteam/SelectiveScrubber/issues/1">a long-standing bug</a> (4 years!).
<h2>Creating extension framework</h2>
To start, we're going to use the simplest extension possible, a project folder containing a file called <code>manifest.json</code>. Add the following into the file:
<pre class="prettyprint" data-filename="manifest.json"><span class="pun">{</span>
    <span class="str">"name"</span><span class="pun">:</span> <span class="str">"My extension"</span><span class="pun">,</span>
    <span class="str">"version"</span><span class="pun">:</span> <span class="str">"1.0.0"</span><span class="pun">,</span>
    <span class="str">"description"</span><span class="pun">:</span> <span class="str">"My extension doesn't do anything yet!"</span><span class="pun">,</span>
    <span class="str">"manifest_version"</span><span class="pun">:</span> <span class="lit">2</span>
  <span class="pun">}</span></pre>
Technically you've now created an extension, congratulations! There's a bit more work to do for it to be actually useful however...
<h2>Preparing Chrome for extension development</h2>
Before any extension code can be run, running local unpacked extensions must be enabled. This is done by navigating to your extensions page (chrome://extensions/), and:
<ol>
 	<li>Toggling "Developer mode" to on in the top right.</li>
 	<li>Selecting the newly visible "Load unpacked" option.</li>
 	<li>Selecting your project folder.</li>
</ol>
Your extension should now be visible in your extensions list! The little "Reload" icon must be clicked after every time you make code changes to make Chrome run the updated code.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/extensionoverview.png"><img class="aligncenter size-medium wp-image-2174" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/extensionoverview-300x159.png" alt="" width="300" height="159" /></a>
<h2>Preparing the manifest</h2>
First, add the following snippet to your <code>manifest.json</code> file, to tell Chrome that we're going to be using a background JavaScript file:
<pre>"background": {
    "persistent": false,
    "scripts": ["js/clear_history.js"]
},</pre>
Next, create a folder called <code>js</code> and add <code>clear_history.js</code> into it. This can be left empty for now.

We're also going to have an options page, so that must be registered in the <code>manifest.json</code> too:
<pre>"options_ui": {
    "page": "options.html",
    "chrome_style": true
}</pre>
Again, create the <code>options.html</code> page in the project folder. After reloading the extension (with the reload button on the extension's card), you should now see a "Inspect views background page" link. Clicking this won't do anything, but it's presence confirms your background script earlier has been registered successfully!
<h2><a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/extensionbackground.png"><img class="aligncenter size-medium wp-image-2175" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/extensionbackground-300x160.png" alt="" width="300" height="160" /></a></h2>
We're also going to need permissions for storage (for preferences) and history (for deleting entries), so add the following into the manifest too:
<pre><span class="pl-s"><span class="pl-pds">"</span>permissions<span class="pl-pds">"</span></span>: [<span class="pl-s"><span class="pl-pds">"</span>history<span class="pl-pds">"</span></span>, <span class="pl-s"><span class="pl-pds">"</span>storage<span class="pl-pds">"</span></span>],</pre>
<h2>Adding toolbar icon</h2>
Next, we're going to add a toolbar icon for our extension, so that we can see it is installed and access the options. To do this, we need an app icon in both 48x48 and 96x96 sizes. I'd recommend using <a href="https://material.io/tools/icons/" target="_blank" rel="noopener">Google's Material Design icon library</a>, and exporting PNG icons in 48dp (this provides both the necessary app sizes). For this tutorial, I'll use <a href="https://material.io/tools/icons/?icon=find_replace&amp;style=baseline" target="_blank" rel="noopener">the <code>find_replace</code> icon</a>.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icon.png"><img class="aligncenter size-medium wp-image-2176" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/icon-300x248.png" alt="" width="300" height="248" /></a>

Once you've got your icons, rename them to <code>icon_48.png</code> and <code>icon_96.png</code>, then place them inside an <code>images</code> folder in your project folder. Now that we've got image assets, edit the <code>manifest.json</code> to include them as icons (for the extension store), and as a <code>browser_action</code> to appear in the toolbar.
<pre>"icons": {
    "48": "images/icon_48.png",
    "96": "images/icon_96.png"
},
"browser_action": {
    "default_icon": "images/icon_96.png"
},</pre>
After reloading the extension, it will now appear in your Chrome toolbar! Right clicking it shows an "Options" menu, which currently... opens an empty page. Time to fix that!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/options.png"><img class="aligncenter size-full wp-image-2177" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/options.png" alt="" width="173" height="150" /></a>
<h2>Creating the options screen</h2>
In this section, we'll be creating the Chrome extension's options, where users can edit their scrub list and toggle a scrub summary message.
<h3>Options HTML</h3>
Open up the <code>options.html</code> file inside the project root. The HTML content is very simple, containing a text area, a checkbox, a button, and a status:
<pre>&lt;html&gt;
    &lt;body&gt;
        &lt;p&gt;Which sites / phrases should be scrubbed? One per line!&lt;/p&gt;
        &lt;textarea id="sites" rows="4" cols="50"&gt;&lt;/textarea&gt;&lt;br&gt;&lt;br&gt;
        &lt;label&gt;&lt;input id="popup" type="checkbox"&gt;Display scrub summary?&lt;/label&gt;&lt;br&gt;&lt;br&gt;
        &lt;button id="save"&gt;Save&lt;/button&gt; &lt;b id="status" /&gt;
        &lt;script src="js/options.js"&gt;&lt;/script&gt;
    &lt;/body&gt;
&lt;/html&gt;</pre>
Accessing the options screen will now show our options! However, since there's no functional code yet, it's not particularly useful.

Create the <code>options.js</code> file inside the <code>js</code> folder, this will contain all of the JavaScript for our options page. The options page contains 2 pieces of functionality; saving preferences, and loading preferences.
<h3>Saving options</h3>
First, an event listener must be added to the "Save" button, so that saving happens when it is pressed. Add the following into <code>options.js</code>:
<pre>document.getElementById('save').addEventListener('click', save);</pre>
Now we need to create the <code>save</code> function that the listener calls. This function gets the list of sites from the text area and the "Display scrub summary?" checkbox answer, then places them in Chrome storage. Note that <code>displaySavedMessage</code> is the function to be called when data has been successfully saved. This function just displays a piece of text in the status field of the page, then removes it after 2 seconds.
<pre>function save() {
    var sitesToSave = document.getElementById('sites').value;
    var displayPopup = document.getElementById('popup').checked;
    chrome.storage.sync.set({
        sites: sitesToSave,
        popup: displayPopup
    }, displaySavedMessage);
}

function displaySavedMessage() {
    var status = document.getElementById('status');
    status.textContent = 'Sites saved.';
    setTimeout(function() {
        status.textContent = '';
    }, 2000);
}</pre>
<h3>Loading options</h3>
Data is now being saved! The next step is to load this data when the options page is opened. This is done using event listeners again:
<pre>document.addEventListener('DOMContentLoaded', load);</pre>
Again, we now need to create the <code>load</code> function. The options are pulled from Chrome storage again, this time with a default site list and popup value. Once options are loaded (default or user-set), the UI is updated accordingly.
<pre>function load() {
    chrome.storage.sync.get({
        sites: 'supersecretsite.com',
        popup: false
    }, function(items) {
        document.getElementById('sites').value = items.sites;
        document.getElementById('popup').checked = items.popup;
    });
}</pre>
Saving and loading data is now complete! This can be tested by changing your options, saving, then reopening the options page. If everything worked, your changes should have persisted.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/saveload.png"><img class="aligncenter size-medium wp-image-2180" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/saveload-300x204.png" alt="" width="300" height="204" /></a>
<h2>Clearing out history when clicked</h2>
For the actual history clearing, we're going to need to open up the <code>clear_history.js</code> file created at the very start.

First, add a listener to the browser button being clicked. When clicked, the options will be loaded, then passed to <code>deleteBySites</code> when ready:
<pre>chrome.browserAction.onClicked.addListener(function(tab) {
    chrome.storage.sync.get({
        sites: 'supersecretsite.com',
        popup: false
    }, deleteBySites);
});</pre>
<code>deleteBySites</code> splits the <code>sites</code> value loaded from Chrome storage, one site per line. <code>numSites</code> is set for later, then for each site <code>deleteBySite</code> is called:
<pre>var numSites = 0;
function deleteBySites(storage) {
    var allSites = storage.sites.split("\n");
    numSites = allSites.length;
    for (var i = 0; i &lt; allSites.length; i++) {
        deleteBySite(allSites[i], storage.popup);
    }
}</pre>
<code>deleteBySite</code> performs a search of Chrome's history for the specified phrase, which returns a list of results. These results are then iterated over, deleting all, as well as keeping a running tally of the number of results deleted. Finally, if the <code>popup</code> option is true, the results are passed to <code>siteDeleted</code>.
<pre>function deleteBySite(site, popup) {
    chrome.history.search({
            text: site,
            startTime: 0,
            maxResults: 999999
        },
        function(results) {
            var itemsDeleted = 0;
            for (itemsDeleted; itemsDeleted &lt; results.length; itemsDeleted++) {
                chrome.history.deleteUrl({
                    url: results[itemsDeleted].url
                });
            }
            if (popup) {
                siteDeleted(site, itemsDeleted);
            }
        });
}</pre>
<code>siteDeleted</code> adds the site and deleted entries count to <code>siteString</code>, then displays a summary if every site has been processed. This is checked by making sure <code>sitesProcessed</code> is equal to (or larger than) the <code>numSites</code> we defined earlier. Finally, this text is shown as a popup alert.
<pre>var sitesProcessed = 0;
var siteString = "Scrub complete:\n";
function siteDeleted(site, count) {
    sitesProcessed++;
    siteString += (count + "x " + site + "\n");
    if (sitesProcessed &gt;= numSites) {
        alert(siteString);
        sitesProcessed = 0;
        siteString = "Scrub complete:\n";
    }
}</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/scrubbed.png"><img class="aligncenter size-medium wp-image-2181" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/scrubbed-300x181.png" alt="" width="300" height="181" /></a>

The extension is now fully working! The only thing left to do is prepare it for release, and upload it.
<h2>Preparing your Chrome extension for release</h2>
Your app's name and description in your <code>manifest.json</code> should now be changed to something unique. Additionally, you might want to set a <code></code> and a <code>version</code>. Whilst <code>version</code> must be numerical, <code>versionName</code> can be anything you want. <a href="https://semver.org/" target="_blank" rel="noopener">Semantic Versioning</a> is a good way of ensuring consistent, understandable version codes.

Finally, right click your extension's folder, and make a <code>.zip</code> archive of it ("Send to" -&gt; "Compressed (zipped) folder").
<h2>Publishing your Chrome extension</h2>
<ol>
 	<li>Go to the beta <a href="https://chrome.google.com/webstore/devconsole/" target="_blank" rel="noopener">Chrome Developer Dashboard</a> (you may need to opt in).</li>
 	<li>Register as a developer, and pay the necessary $5 registration fee (to prevent spam).</li>
 	<li>Click "New item" in the top right, and select your <code>.zip</code> file.</li>
 	<li> You will then have a draft extension, which needs the following fields to be filled in before publishing:
<ol>
 	<li>Extension's language</li>
 	<li>Extension's primary category</li>
 	<li>1-5 screenshots (1280x800 or 640x400)</li>
</ol>
</li>
</ol>
Once these 3 fields have been filled in, your extension is ready to publish! However, you'll likely want to customise the support URLs, app icons, detailed description, and a few other pieces of metadata. When you're ready to go, press "Publish item" in the top right.

<strong>Congratulations, it's published!</strong>

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/dashboard.png"><img class="aligncenter size-medium wp-image-2186" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/dashboard-300x218.png" alt="" width="300" height="218" /></a>

This entire project is <a href="https://github.com/JakeSteam/SelectiveScrubber">available as a repository</a>, and the <a href="https://chrome.google.com/webstore/detail/ljjdcbfpdmjelppcfeiadnjagdhompkb/">finished extension can be installed here</a>.

Additional resources:
<ul>
 	<li><a href="https://developer.chrome.com/webstore/get_started_simple" target="_blank" rel="noopener">Simple getting started guide by Google</a></li>
 	<li><a href="https://developer.chrome.com/webstore/publish" target="_blank" rel="noopener">Complete guide to publishing by Google</a></li>
</ul>