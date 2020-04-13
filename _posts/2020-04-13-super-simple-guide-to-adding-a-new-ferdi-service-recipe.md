---
ID: 2730
post_title: >
  Super simple guide to adding a new Ferdi
  service recipe
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/super-simple-guide-to-adding-a-new-ferdi-service-recipe/
published: true
post_date: 2020-04-13 14:00:59
---
In my last post I gave a brief overview of the <a href="https://getferdi.com/" target="_blank" rel="noopener noreferrer">messaging browser Ferdi</a>, and why I love it so much. I noticed there wasn't a recipe for <a href="https://nextdoor.co.uk/" target="_blank" rel="noopener noreferrer">Nextdoor</a> messages yet, so I decided to create it!

I found <a href="https://github.com/getferdi/recipes/blob/master/docs/integration.md" target="_blank" rel="noopener noreferrer">the documented process</a> a little confusing, hopefully this guide helps others avoid the same issues I ran into. The finished recipe <a href="https://github.com/JakeSteam/ferdi-nextdoor" target="_blank" rel="noopener noreferrer">can be downloaded here</a>, and should be in the next update of Ferdi. I'm using Windows, but instructions are provided for Mac &amp; Linux.

<!--more-->
<h2>Making your Ferdi recipe</h2>
<h3>Getting started</h3>
Whilst it is possible to create a recipe from scratch, it's far easier to use an existing one as a starting point. Just navigate to Ferdi's recipes in one of the following locations:
<ul>
 	<li>Mac: <code>~/Library/Application Support/Ferdi/recipes/</code></li>
 	<li>Windows: <code>%appdata%/Ferdi/recipes/</code></li>
 	<li>Linux: <code>~/.config/Ferdi/recipes/</code></li>
</ul>
Once you're there, create a folder called "dev", and copy one of the existing recipe folders into it. I used Discord, as it seemed to have simpler config files than my other recipes. Here's how your <code>recipes</code> folder should look, with a copied folder inside <code>dev</code>:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/RBjHnOs.png"><img class="aligncenter wp-image-2733 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/RBjHnOs.png" alt="" width="555" height="207" /></a>
<h3>Creating your recipe</h3>
This is going to be the bulk of the work! Whilst the 6 required files can be confusing initially, they're actually very straight forward. I'd strongly recommend <a href="https://github.com/JakeSteam/ferdi-nextdoor" target="_blank" rel="noopener noreferrer">looking at my finished recipe</a> if any steps seem unclear!

Rename your copied folder to the name of your service (in my case "nextdoor") before making any changes. Then, update each file to work with your new service.

<strong>README.md</strong>: This is just a simple text file to describe your recipe. Just update the service name in here in any text editor.

<strong>icon.png / icon.svg</strong>: These two files (a 1024x1024 PNG and transparent SVG) are used in various places throughout Ferdi to represent your recipe. If you're making a recipe for a large company, they're likely to have a helpful "Brand assets" page on their site. For example, Facebook Messenger <a href="https://en.facebookbrand.com/messenger/assets/messenger/" target="_blank" rel="noopener noreferrer">provides both the necessary files</a>. If you can't find any prepared downloads, you may need to find the resources elsewhere. I found <a href="https://vecta.io/symbols/category/brands-logo" target="_blank" rel="noopener noreferrer">Vecta</a> extremely helpful!

<strong>package.json</strong>: This file contains the metadata about your recipe. You'll almost certainly just need to update the <code>id</code>, <code>name</code>, <code>description</code>, <code>author</code> and <code>serviceURL</code> to more appropriate values. You may also need to update <code>hasNotificationSound</code> to avoid double notifying users. There's a detailed explanation of all possible fields <a href="https://github.com/getferdi/recipes/blob/master/docs/configuration.md" target="_blank" rel="noopener noreferrer">on the Ferdi wiki</a>.

<strong>index.js</strong>: This file contains the JavaScript that Ferdi itself should run, NOT on the site itself. This probably doesn't need anything more than the minimal configuration:
<pre>"use strict";
module.exports = Franz =&gt; Franz;</pre>
<strong>webview.js</strong>: This is the file that handles the JavaScript for actually listening for notifications. Whilst this will be different for every site, essentially the goal is to find an HTML element on the screen that contains the number of notifications. The number of notifications can then be pulled from this element. For Nextdoor, there is a <code>notification-badge</code> element that only appears if there are unread notifications. If the element isn't present, there are 0 notifications.

This is the Nextdoor script, it needs to be customised (via trial and error in the Chrome console) for your recipe!
<pre>"use strict";

module.exports = Franz =&gt; {
  const getMessages = function getMessages() {
    var unread = 0
    const notificationBadge = document.getElementsByClassName('notification-badge')[0]
    if (notificationBadge != undefined) {
        unread = notificationBadge.innerText;
    }
    Franz.setBadge(parseInt(unread, 10));
  };

  Franz.loop(getMessages);
};</pre>
<h3>Loading into Ferdi<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/LKmK4TP.png"><img class="size-medium wp-image-2735 alignright" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/LKmK4TP-300x246.png" alt="" width="300" height="246" /></a></h3>
Now that you have the first draft of your custom recipe inside the <code>dev</code> folder, it can be loaded into Ferdi:
<ol>
 	<li>Restart Ferdi.</li>
 	<li>Open Ferdi settings.</li>
 	<li>Go to "Available services" tab.</li>
 	<li>Go to "Custom Services" tab.</li>
 	<li>Your recipe should show up under "Community 3rd Party Recipes", click it and it'll be added like any other service!</li>
</ol>
<h2>Publishing your Ferdi recipe</h2>
To get your new recipe into the main Ferdi repository to others can use it, it has to be properly published, packaged, and reviewed.
<h3>Uploading recipe repository</h3>
Your recipe has to be uploaded to a public GitHub repository, there's <a href="https://help.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line" target="_blank" rel="noopener noreferrer">many guides</a> if you haven't done this before. Your repository should look <a href="https://github.com/JakeSteam/ferdi-nextdoor" target="_blank" rel="noopener noreferrer">like this</a> once done.
<h3>Running packaging scripts</h3>
<ol>
 	<li>Fork the <a href="https://github.com/getferdi/recipes" target="_blank" rel="noopener noreferrer">Ferdi recipes repository</a> (<a href="https://help.github.com/en/github/getting-started-with-github/fork-a-repo" target="_blank" rel="noopener noreferrer">here is a guide</a> if needed).</li>
 	<li>Check out your forked repository.</li>
 	<li>If you don't have <a href="https://classic.yarnpkg.com/en/docs/install/" target="_blank" rel="noopener noreferrer">Yarn</a> already installed (I didn't), install it with the default options.</li>
 	<li>Open a terminal / Powershell / Command prompt in the <code>/scripts/</code> folder of your forked repo.</li>
 	<li>Run <code>yarn install</code> to run all of the dependencies needed for prepare the packaging script. You should see <a href="https://i.imgur.com/SDj7siV.png" target="_blank" rel="noopener noreferrer">a "Done" message</a>.</li>
 	<li>Run <code>yarn github &lt;your recipe repo's URL&gt;</code> to package your recipe. You should see <a href="https://i.imgur.com/X6C07X3.png" target="_blank" rel="noopener noreferrer">a "Done" message</a> again.</li>
 	<li>Your fork of the Ferdi recipes repository should now have 6 new files (your recipe), and 1 modified file (the metadata storage). <a href="https://i.imgur.com/KMrvh40.png" target="_blank" rel="noopener noreferrer">This is how it looks in GitKraken</a>.</li>
 	<li>Commit these files, then push your repository.</li>
</ol>
<h3>Raising PR</h3>
You now have a version of Ferdi's recipes repo with your changes, which need to be approved.

Just raise a PR against the repo (<a href="https://github.com/getferdi/recipes/pull/94" target="_blank" rel="noopener noreferrer">something like this</a>, here's <a href="https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request" target="_blank" rel="noopener noreferrer">a guide</a>), and one of the project's maintainers will take a look and check your work. Any issues will be raised, or the code will be merged.

You've contributed to an open source project, congrats!