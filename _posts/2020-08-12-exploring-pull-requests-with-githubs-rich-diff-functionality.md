---
ID: 2803
post_title: 'Exploring pull requests with GitHub&#8217;s rich diff functionality'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/exploring-pull-requests-with-githubs-rich-diff-functionality/
published: true
post_date: 2020-08-12 15:15:52
---
Like many developers, I spend a surprisingly large amount of time reviewing other people's code. In fact, according to GitHub that's around 20% of my day!<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/A7EFN1q.png">
<img class="size-medium wp-image-2804 alignright" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/A7EFN1q-300x251.png" alt="" width="300" height="251" /></a>

When reviewing pull requests, code changes are very easy to review and approve. However, comparing images can be much harder to check, since by default GitHub only shows an unintuitive "Binary file not shown" message. Luckily, I recently discovered the rarely mentioned "rich diff" feature that makes this so, so much easier. I'll briefly run through the functionality it offers, all screenshots in this post are from <a href="https://github.com/JakeSteam/RichDiffExperiments" target="_blank" rel="noopener noreferrer">my Rich Diff repo</a>. <!--more-->
<h2>Overall</h2>
First of all, how do you access rich diff? Simple, click the "page" icon next to many file types! Once clicked, this will compare the two files in a much more helpful way. If no "page" icon is visible, rich diff is not available.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/dCv3J6o.png"><img class="aligncenter size-full wp-image-2807" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/dCv3J6o.png" alt="" width="734" height="95" /></a>
<h2>Image rich diff</h2>
Images are probably the best use cases for rich diff. I created a collection of test images (SVG, PNG, JPG, GIF, BMP), committed them, and <a href="https://github.com/JakeSteam/RichDiffExperiments/commit/2fff7598b7ddc2b58bacc0e6e6860c8d5c5b4656" target="_blank" rel="noopener noreferrer">then committed</a> new versions of each.

I found SVGs, PNGs, JPGs, and GIFs can use rich diff, but BMPs cannot. For the compatible file types, there are 3 options for the rich diff:
<h3>2-up<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/3qjoZ8A.png"><img class="alignright wp-image-2808 size-thumbnail" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/3qjoZ8A-150x150.png" alt="" width="150" height="150" /></a></h3>
This mode displays the images side by side, as well as their width, height, and file size. This is a great way to notice an image has slightly changed dimensions, since this might not be noticeable with the naked eye.

Unfortunately, this mode does not help when identifying small visual changes within the image itself, the other modes are better for that.
<h3>Swipe<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/Rjf3nY8.png"><img class="alignright size-thumbnail wp-image-2811" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/Rjf3nY8-150x150.png" alt="" width="150" height="150" /></a></h3>
This mode displays the two versions of the image with a left to right slider over the top.

This is an excellent way of noticing small changes within an image, whilst still keeping track of the overall image.
<h3>Onion Skin<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/u2wqvqB.png"><img class="alignright size-thumbnail wp-image-2813" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/u2wqvqB-150x150.png" alt="" width="150" height="150" /></a></h3>
This mode overlays the new image over the old one, and lets you control the transparency.

Just like "Swipe", this lets you easily see small changes, whilst also being able to see the entirety of both images at all times.
<h2>Document rich diff</h2>
Whilst images performed well, I was curious if common document types would work. In a word... no.

As before, I created &amp; <a href="https://github.com/JakeSteam/RichDiffExperiments/commit/33ea6257db94e9f41d4ce4754fe99b1140bff5e1" target="_blank" rel="noopener noreferrer">updated a selection of common formats</a> (DOC, DOCX, HTML, PDF, RTF, MD), and only MD (markdown) had rich diff. The RTF and HTML files <em>did</em> show the code changes, but this isn't particularly helpful for a verbose format like RTF!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/KUBjqor.png"><img class="aligncenter wp-image-2818 size-large" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/KUBjqor-1024x329.png" alt="" width="700" height="225" /></a>

The Markdown file fared much better, showing a very helpful visual indicator of what has been added and removed.

The coloured bars on the left indicate the line status (first line changed, second line added). The coloured highlights in the changed line indicate the removed and added sections, in an easy to understand format.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/FOya3Dn.png"><img class="aligncenter wp-image-2821 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/FOya3Dn.png" alt="" width="764" height="233" /></a>

Whilst this indicator usually works well, <a href="https://github.com/Aircoookie/WLED/commit/35098c474cecaff316bccab7e6bf925a03ef8fbe#diff-0730bb7c2e8f9ea2438b52e419dd86c9" target="_blank" rel="noopener noreferrer">in some commits</a> it instead shows an entire section as changed instead of only the specific lines.
<h2>Summary</h2>
In summary, rich diff is an extremely powerful feature that is nowhere near as well known as it should be. However, the inability to enable it by default, or to set a default image comparison type both make it a bit awkward to use.

One likely reason for the feature not being heavily promoted is the extra processing time on GitHub's side required for a rich diff. At least in 2016, larger Markdown files could <a href="https://github.com/cabforum/documents/issues/27" target="_blank" rel="noopener noreferrer">take up to 5 seconds</a>, which is GitHub's rich diff timeout.

Hopefully this post helps you next time you need to compare changed images in a GitHub pull request. It was a lifesaver when I recently needed to look at <a href="https://github.com/chris-sloan" target="_blank" rel="noopener noreferrer">a colleague</a>'s PR containing 180 JPGs!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/Z5uYxKj.png"><img class="aligncenter size-full wp-image-2822" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/08/Z5uYxKj.png" alt="" width="501" height="215" /></a>

&nbsp;