---
ID: 2486
post_title: >
  Using Dyno to create a Discord command
  that displays a message and DMs a
  specified user
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-dyno-to-create-a-discord-command-that-displays-a-message-and-dms-a-specified-user/
published: true
post_date: 2019-05-20 12:09:06
---
Dyno is an extremely powerful bot for Discord, with a staggering set of features, split into modules. One of the easiest to use is the "Custom Command" module, allowing actions to be taken in response to messages in chat. Setting up Dyno to a server you have "Manage server" permissions on is very straightforward, just click "Add to server" on <a href="https://dyno.gg/account" target="_blank" rel="noopener noreferrer">Dyno.gg</a>, then follow the instructions.

On the <a href="https://discord.gg/D2cNrqX" target="_blank" rel="noopener noreferrer">Android Dev</a> and <a href="https://discord.gg/N5MDVG6" target="_blank" rel="noopener noreferrer">Discord Dev</a> servers, there are a few commands currently in use. Most of them are simple autoresponses (displaying a message when a command like <code>?screenshot</code> is entered), but the longer autoresponses take up far too much room in chat. To get around this, I implemented a solution that DMs the response to a specified user, and displays a confirmation message in the original channel.<!--more-->
<h2>Command code</h2>
Here's the code used on the <a href="https://discord.gg/D2cNrqX" target="_blank" rel="noopener noreferrer">Android Dev</a> discord to let users send each other information on Kotlin, using <code>?kotlin @Username#123</code>:
<pre>{delete}{!announce {channel} Sending Kotlin resources to $1 on behalf of {user.mention}!} 
{dm:$1} 
Looking to get started with Kotlin development? 
Here's a Udacity course: &lt;https://www.udacity.com/course/developing-android-apps-with-kotlin--ud9012&gt; 
And a useful wiki if you already know Java: &lt;https://github.com/Zhuinden/guide-to-kotlin/wiki&gt;</pre>
The command can look a little complicated, so here's what each part does:
<ul>
 	<li>The curly braces (<code>{</code> &amp; <code>}</code>) wrap each part of the command.</li>
 	<li><code>delete</code> deletes the command (<code>?kotlin</code>) used to summon the bot.</li>
 	<li><code>!announce</code> uses Dyno's <code>announce</code> command to display the "X has sent resources to Y" message in chat.
<ul>
 	<li><code>channel</code> tells Dyno to output the message in the channel where the command was sent.</li>
 	<li><code>$1</code> tells Dyno to use the first parameter sent to it, in this case the target user (X).</li>
 	<li><code>user.mention</code> references the user who sent the initial command (Y).</li>
</ul>
</li>
 	<li><code>dm:$1</code> sends a private message (DM) to <code>$1</code>, which is described above.</li>
 	<li>Finally, <code>&lt;</code> and <code>&gt;</code> are used around links to prevent them displaying large embeds.</li>
</ul>
Here's what displays in the channel:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-115614.jpg"><img class="aligncenter size-full wp-image-2498" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-115614.jpg" alt="" width="596" height="163" /></a>

And here's what the user receives in their inbox:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-115753.jpg"><img class="aligncenter size-full wp-image-2500" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-115753.jpg" alt="" width="946" height="216" /></a>

This approach unfortunately doesn't work if a user has blocked Dyno. However, this is relatively unusual it's generally not worth considering as a downside.

Overall the approach is simple to adapt and extend, and adds powerful functionality to any server. If your servers uses commands like this, make sure it also has a <code>#botspam</code> channel for people to try out the commands!

Finally, here's how the command will look on your Dyno Custom Commands list:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-120517.jpg"><img class="aligncenter size-full wp-image-2501" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/05/Annotation-2019-05-20-120517.jpg" alt="" width="1303" height="217" /></a>