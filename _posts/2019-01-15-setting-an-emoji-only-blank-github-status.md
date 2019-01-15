---
ID: 2340
post_title: >
  Setting an emoji only / blank GitHub
  status
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/setting-an-emoji-only-blank-github-status/
published: true
post_date: 2019-01-15 22:28:05
---
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/7x8Asov.png"><img class="size-full wp-image-2343 alignright" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/7x8Asov.png" alt="" width="205" height="210" /></a>Recently GitHub added user statuses, allowing you to add a little emoji and bit of text under your name on your profile. This is changed either on your profile or in the top right of GitHub, as seen in the screenshot.

As with other services (e.g. Slack), I wanted to just set an emoji as my status, without any unnecessary text. However, not entering any text (or just a space) in the status field isn't possible as the "Set status" button is disabled!<!--more-->

To get around this, copy the character between these two arrows --&gt;⠀&lt;-- and paste it into your status. Tada, empty status!

For example, here's how it looks on my profile:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/OvdQF5G.png"><img class="aligncenter size-medium wp-image-2342" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/OvdQF5G-300x245.png" alt="" width="300" height="245" /></a>

For those curious, this works because the character between the arrows <em>looks</em> like a space, but isn't one. It's actually the <a href="https://en.wiktionary.org/wiki/%E2%A0%80">braille character for "no dots"</a>, and since it's part of unicode should work across all platforms that support multiple language sets.

&nbsp;