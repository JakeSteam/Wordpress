---
ID: 2754
post_title: Posting a Slack message from Travis CI
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/posting-a-slack-message-from-travis-ci/
published: true
post_date: 2020-04-27 16:00:09
---
As part of my ongoing mission to rewrite our CI system, the final step is letting QA know that a new build is available. They can't test it if they don't know it's there!

We'll be using our CI's bash script for this, <a href="https://blog.jakelee.co.uk/creating-an-app-bundle-and-apk-on-travis-ci-server/" target="_blank" rel="noopener noreferrer">the start of this article</a> describes how to create one if you haven't already.

<!--more-->

Not interested in the full tutorial? <a href="https://gist.github.com/JakeSteam/671658a8654b0ab19b61cfa9e9c100c9" target="_blank" rel="noopener noreferrer">Here's the final Gist</a>, and here's what we'll end up with:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/tgzBJ6o.png"><img class="aligncenter size-full wp-image-2756" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/tgzBJ6o.png" alt="" width="510" height="214" /></a>
<h2>Formatting a Slack message</h2>
The first step is telling Slack how our message should look. Slack has a <a href="https://api.slack.com/tools/block-kit-builder?template=1" target="_blank" rel="noopener noreferrer">really nice online visualiser</a> for messages using their "Block Kit" syntax. Using one of their templates, it's very easy to quickly build a message with example data and preview it on desktop or mobile.

<a href="https://api.slack.com/tools/block-kit-builder?mode=message&amp;blocks=%5B%7B%22type%22%3A%22section%22%2C%22text%22%3A%7B%22type%22%3A%22mrkdwn%22%2C%22text%22%3A%22*%24BRANCH%20build%20(%23%24BUILD_NUMBER)%20available%20on%20devices!*%5Cn*Message%3A*%20%24COMMIT_MESSAGE%22%7D%7D%2C%7B%22type%22%3A%22section%22%2C%22fields%22%3A%5B%7B%22type%22%3A%22mrkdwn%22%2C%22text%22%3A%22*Debuggable%3A*%5Cn%24DEBUGGABLE%22%7D%2C%7B%22type%22%3A%22mrkdwn%22%2C%22text%22%3A%22*Hash%3A*%5Cn%24COMMIT_HASH_SHORT%22%7D%5D%7D%2C%7B%22type%22%3A%22actions%22%2C%22elements%22%3A%5B%7B%22type%22%3A%22button%22%2C%22text%22%3A%7B%22type%22%3A%22plain_text%22%2C%22text%22%3A%22Install%22%7D%2C%22value%22%3A%22internal-app-sharing%22%2C%22url%22%3A%22https%3A%2F%2Fexample.com%22%7D%2C%7B%22type%22%3A%22button%22%2C%22text%22%3A%7B%22type%22%3A%22plain_text%22%2C%22text%22%3A%22Help%22%7D%2C%22value%22%3A%22help%22%2C%22url%22%3A%22https%3A%2F%2Fsupport.google.com%2Fgoogleplay%2Fandroid-developer%2Fanswer%2F9303479%23on%22%7D%2C%7B%22type%22%3A%22button%22%2C%22text%22%3A%7B%22type%22%3A%22plain_text%22%2C%22text%22%3A%22Travis%22%7D%2C%22value%22%3A%22travis%22%2C%22url%22%3A%22https%3A%2F%2Fexample.com%22%7D%2C%7B%22type%22%3A%22button%22%2C%22text%22%3A%7B%22type%22%3A%22plain_text%22%2C%22text%22%3A%22GitHub%22%7D%2C%22value%22%3A%22github%22%2C%22url%22%3A%22https%3A%2F%2Fgithub.com%2F%24REPO%2Fcommit%2F%24COMMIT_HASH%22%7D%5D%7D%5D" target="_blank" rel="noopener noreferrer">Here is the template</a> I created, it might be helpful as a starting point for your own.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/oPB3nFh.png"><img class="aligncenter size-large wp-image-2757" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/oPB3nFh-1024x480.png" alt="" width="700" height="328" /></a>

In my messages, I display metadata QA / other devs might find useful: branch name, build number, commit message, commit hash, debuggable state.

I've also included 4 buttons. One for the Google Play Internal App Sharing link (<a href="https://blog.jakelee.co.uk/uploading-an-app-bundle-to-google-play-internal-app-sharing-from-travis-ci/" target="_blank" rel="noopener noreferrer">guide to setting it up</a>), one for help installing, one for the Travis build, and one for the commit's link on GitHub.
<h2>Getting a Slack webhook</h2>
Slack has <a href="https://api.slack.com/messaging/webhooks" target="_blank" rel="noopener noreferrer">an official guide</a> to creating a webhook URL, but it is much more complicated than necessary. The essential steps are:
<ol>
 	<li>Create <a href="https://api.slack.com/apps/new" target="_blank" rel="noopener noreferrer">a new app</a>.</li>
 	<li>Add the "Incoming WebHooks" app to your Slack workspace by clicking "Add to Slack" from <a href="https://api.slack.com/apps" target="_blank" rel="noopener noreferrer">the app management page</a> (this will be "Request Configuration" if you're not an admin).</li>
 	<li>Select which room the messages should go to (<a href="https://i.imgur.com/RspYoeQ.png" target="_blank" rel="noopener noreferrer">picture</a>). It might be helpful setting this to a private message with yourself whilst testing!</li>
 	<li>Once added, scroll down to "Integration settings" (<a href="https://i.imgur.com/4qGPM7X.png" target="_blank" rel="noopener noreferrer">picture</a>), and customise the name and avatar (emoji or uploaded image).</li>
 	<li>Copy the "Webhook URL".</li>
 	<li>Save settings.</li>
</ol>
<h2>Posting messages to Slack</h2>
From your existing bash script (<a href="https://blog.jakelee.co.uk/creating-an-app-bundle-and-apk-on-travis-ci-server/" target="_blank" rel="noopener noreferrer">guide to setting it up</a> if you need to), we just need to do a simple POST to our webhook. This assumes you've defined your Slack message's JSON (<code>$DATA</code>) &amp; webhook URL (<code>$WEBHOOK_URL</code>) already:
<pre>HTTP_RESPONSE=$(curl --silent --write-out "HTTPSTATUS:%{http_code}" \
    --header "Content-type: application/json" \
    --request POST \
    --data "$DATA" \
    "$WEBHOOK_URL")
HTTP_BODY=$(echo ${HTTP_RESPONSE} | sed -e 's/HTTPSTATUS\:.*//g')
HTTP_STATUS=$(echo ${HTTP_RESPONSE} | tr -d '\n' | sed -e 's/.*HTTPSTATUS://')
if [[ ${HTTP_STATUS} != 200 ]]; then
  echo -e "Sending Slack notification failed.\nStatus: $HTTP_STATUS\nBody: $HTTP_BODY\nExiting."
  exit 1
fi</pre>
This short script will send the custom message to Slack, and display any errors if they occur.
<h2>Putting it all together</h2>
You should now have everything you need to create a function that you can invoke in your scripts.

We'll put a few placeholders (e.g. <code>$BRANCH</code>) in our message, and replace them when the script runs. I'm defining all of my environment variables at the start, so the script can be easily migrated to another CI if necessary, but some variables still need to be passed to the function.

<a href="https://gist.github.com/JakeSteam/671658a8654b0ab19b61cfa9e9c100c9" target="_blank" rel="noopener noreferrer">Here's the full Gist</a>, make sure you define / pass all your variables!
<h2>Further improvements</h2>
It might be useful to tag user(s) in your message. This can be done by adding their user ID somewhere in your message in this format: <code>&lt;@theiruserid&gt;</code>, which can be found by:
<ol>
 	<li>Clicking the user whose ID you want to find.</li>
 	<li>Clicking "View full profile"</li>
 	<li>Pressing the 3 dot icon, you can then copy their member ID:</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/iO1uMIo.png"><img class="aligncenter size-full wp-image-2760" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/iO1uMIo.png" alt="" width="475" height="295" /></a>

Another improvement would be stopping the "warning" icon that appears next to a clicked button. This appears because Slack is trying to transfer (empty) data to the arbitrary URL we're using, which isn't accepting it.

Unfortunately there doesn't seem to be an easy fix, since we're using simple webhooks and <a href="https://github.com/slackapi/node-slack-sdk/issues/869" target="_blank" rel="noopener noreferrer">not anything more advanced</a>.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/XFNE0x1.png"><img class="aligncenter size-full wp-image-2761" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/04/XFNE0x1.png" alt="" width="478" height="178" /></a>

&nbsp;