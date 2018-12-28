---
ID: 2265
post_title: >
  Using ShareX to take screenshots and
  upload them to a private Imgur album
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/using-sharex-to-take-screenshots-and-upload-them-to-a-private-imgur-album/
published: true
post_date: 2018-12-28 14:58:20
---
The ability to quickly share screenshots with others is essential to day to day conversation / work, especially when talking to people in other offices or countries. The built in Windows Snipping Tool has good basic functionality (e.g. press Win + Shift + S to select an area to copy), but lacks any image uploading functionality, or advanced editing.

This tutorial will let you do the following with ShareX, with one keypress:
<ol>
 	<li>Select an area</li>
 	<li>Perform advanced editing (numbering, blurring, pixelating, highlighting, circling etc)</li>
 	<li>Copy the selected area to your clipboard, ready to be pasted</li>
 	<li>Upload the selected area to a private album on Imgur, and provide you a link in a notification if you need it</li>
</ol>
Pretty good, for a completely free setup!

<!--more-->
<h2>Installing ShareX</h2>
First, <a href="https://getsharex.com/" target="_blank" rel="noopener">download and install ShareX</a>. It's also <a href="https://store.steampowered.com/app/400040/ShareX/" target="_blank" rel="noopener">available on Steam</a>.

It has an absolutely staggering number of features (see gif below), and it's definitely worth having a look at the included tools if you're ever looking for a new utility program.

[caption id="attachment_2268" align="aligncenter" width="836"]<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/ShareX_Animation.gif"><img class="size-full wp-image-2268" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/ShareX_Animation.gif" alt="" width="836" height="461" /></a> Animation from getsharex.com[/caption]

Once ShareX is running, it will appear in your toolbar. Double clicking the icon opens the settings screen. Whilst it can be a little confusing to navigate, the following steps will cover all you need for this configuration.
<h2>Configuring clipboard copying</h2>
Clipboard copying is going to be an "After capture" task. To enable it, click "After capture tasks", then "Copy image to clipboard", making sure nothing else is ticked. That's it!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/Ow1oJ1S.png"><img class="aligncenter size-full wp-image-2269" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/Ow1oJ1S.png" alt="" width="382" height="144" /></a>
<h2>Configuring Imgur uploading</h2>
The next stage is uploading your screenshot to imgur, so that it displays in a popup notification. Clicking this notification opens the image in your default browser, so you can easily copy the URL wherever you need to.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/M36rzzD.png"><img class="aligncenter size-full wp-image-2270" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/M36rzzD.png" alt="" width="380" height="158" /></a>
<h3>Enabling Imgur</h3>
First, under "After capture tasks", enable "Upload image to host".

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/52vtvJi.png"><img class="aligncenter size-full wp-image-2271" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/52vtvJi.png" alt="" width="393" height="420" /></a>

Next, set Imgur as your image host under "Destinations", "Image Uploader", then "Imgur".

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/nibVcSy.png"><img class="aligncenter size-full wp-image-2272" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/nibVcSy.png" alt="" width="566" height="145" /></a>
<h3>Connecting ShareX to Imgur</h3>
In the same "Destinations" menu, open "Destination settings". You'll now need to authorise ShareX to access your Imgur account. This is done by:
<ol>
 	<li>Select "Account type" of "User".</li>
 	<li>Click "Step 1: Open authorize page".</li>
 	<li>This will open your browser. Log in to your Imgur account, and you will be shown a verification code.</li>
 	<li>Copy this code into the "Verification code" text box in ShareX.</li>
 	<li>Click "Step 2: Complete authorization".</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/x8Eqj42.png"><img class="aligncenter size-medium wp-image-2273" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/x8Eqj42-300x190.png" alt="" width="300" height="190" /></a>

You're now connected! Time to get an album set up.
<h3>Uploading ShareX images to an Imgur album</h3>
Make sure "Use direct link" is ticked, then make ShareX upload your images into an album:
<ol>
 	<li>Make a new album on imgur with the privacy "Secret", so that only you can view it.</li>
 	<li>Click "Refresh album list" on the Imgur destination settings in ShareX.</li>
 	<li>Select your newly created folder.</li>
 	<li>Make sure "Upload images to selected album" is ticked.</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/DY39iIB.png"><img class="aligncenter size-medium wp-image-2274" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/DY39iIB-300x280.png" alt="" width="300" height="280" /></a>
<h2>Adding hotkey</h2>
Almost there! The final necessary step is setting up a hotkey for your task. Set this under "Hotkey settings", I find F11 to be most convenient but it can be almost anything.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/tcKRfCS.png"><img class="aligncenter wp-image-2275 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/tcKRfCS.png" alt="" width="608" height="171" /></a>

Your ShareX is now fully setup! Press F11 (or your hotkey), and enjoy your clipboard copying and automatic uploading!
<h2>Using ShareX's screenshot tool</h2>
When your hotkey is pressed, the ShareX screenshot tool will popup. Whilst just selecting an area will perform the normal actions, there's a lot of functionality included which can be very useful. Below is a screenshot of the editor, as well as an explanation of every option, since it can be a little overwhelming.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/editor.png"><img class="aligncenter size-full wp-image-2279" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/editor.png" alt="" width="641" height="330" /></a>
<h4>Main bar (left to right)</h4>
<ol>
 	<li>The 3 dot icon lets you move the main bar around the screen.</li>
 	<li>The rectangular icon lets you select a rectangular area to capture (default).</li>
 	<li>The circular icon lets you select a circular / oval area to capture.</li>
 	<li>The odd shaped icon lets you select a custom shape to capture. This can have many points, even overlapping!</li>
 	<li>This rectangular icon lets you select a rectangle to overlay on the screen. Selecting this (or the circular overlay) will display options for outline / fill colour.</li>
 	<li>This circular icon lets you select a circular / oval shape to overlay on the screen.</li>
 	<li>The pen icon lets you draw / write on the captured area.</li>
 	<li>The connected dot icon lets you draw lines between areas in the captured area.</li>
 	<li>The arrow icon lets you draw arrows.</li>
 	<li>The letter "A" lets you add text to the captured area.</li>
 	<li>The letter "A" with a background lets you add text, but with a background.</li>
 	<li>The speech bubble lets you add a speech bubble to the screenshot.</li>
 	<li>The "001" icon lets you start counting. Once enabled, each time you click a new number will appear, one higher than the one before. This is useful for listing steps in a process.</li>
 	<li>The folder icon lets you overlay an existing image into your screenshot.</li>
 	<li>The monitor icon lets you overlay a part of the screen on your screenshot. Yes, really!</li>
 	<li>The emoji icon lets you add a sticker. Google's blob emojis and a few custom ones are included. The <a href="https://github.com/ShareX/ShareX/tree/master/ShareX.ScreenCaptureLib/Stickers/BlobEmoji" target="_blank" rel="noopener">full list is in the source code</a>.</li>
 	<li>The pointer icon lets you display a cursor on your screenshot.</li>
 	<li>The striped area icon lets you blur a selected area.</li>
 	<li>The grid icon lets you pixelate a selected area.</li>
 	<li>The "abc" icon lets you highlight a selected area.</li>
 	<li>This option (and the next) provide options for the currently selected tool, so vary depending on tool.</li>
 	<li>See above.</li>
 	<li>The camera icon lets you change the capture area (screen, area, window).</li>
 	<li>The settings icon lets you customise the editor with options for crosshair, icons to display, etc.</li>
 	<li>Same as #1.</li>
</ol>
<h4>Additional UI</h4>
<ol>
 	<li>The "crosshair" icon determines where your cursor currently is. Clicking and holding will let you select an area, whilst a single click will upload the whole screen.</li>
 	<li>The "magnifier" lets you zoom in on where your cursor is, to make sure you are getting the perfect pixel.</li>
 	<li>The X and Y coordinates below your magnifier shown help orientate your cursor in the overall window.</li>
 	<li>Additionally, selecting an area shows the below UI, with your cursor's original X + Y coord, and the selected areas width and height.</li>
</ol>
<h2><a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/ofM9S7t.png"><img class="aligncenter size-full wp-image-2277" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/ofM9S7t.png" alt="" width="261" height="133" /></a>Summary</h2>
Hopefully you've now got your ShareX setup with some basic but useful functionality. There's a <strong>lot</strong> more customisation available, this is barely scratching the surface, so experiment by yourself! There is some community support available on <a href="https://steamcommunity.com/app/400040/discussions/" target="_blank" rel="noopener">the Steam forums</a> and on <a href="https://www.reddit.com/r/sharex">/r/ShareX</a>.

There's also lots of included tools, many with further customisation, such as those screenshotted below. Gifs can also be recorded, images saved locally, and all kinds of convoluted workflows setup! I use ShareX for all screenshots on this blog, and find it extremely useful, usually capturing 10-30 images per day.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/9Z5oPfp.png"><img class="aligncenter size-full wp-image-2282" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/9Z5oPfp.png" alt="" width="340" height="343" /></a>

&nbsp;

&nbsp;