---
ID: 2386
post_title: >
  How to share dependencies in a
  multi-module Android app
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-share-dependencies-in-a-multi-module-android-app/
published: true
post_date: 2019-02-22 17:00:52
---
<h6><em>Note: This post is a tidied up version of <a href="https://stackoverflow.com/a/54173703/608312" target="_blank" rel="noopener">my answer to a StackOverflow question</a> about structuring multi-module apps.</em></h6>
Transitioning to a multi-module project is an excellent way to increase code reuse, optimise build times (unchanged modules aren't recompiled), and ensure your app's structure is logical. Your core goal is to make your <code>app</code>module as small as possible, as it will be recompiled every time.

We used a few general principles, which may help you:

<!--more-->
<ul>
 	<li>A common <code>base-ui</code> module contains the primary <code>strings.xml</code>, <code>styles.xml</code> etc.</li>
 	<li>Other front-end modules (<code>profile</code>, <code>dashboard</code>, etc) implement this <code>base-ui</code> module.</li>
 	<li>Libraries that will be used in <em>all</em> user-facing modules are included in <code>base-ui</code>, as an <code>api</code> instead of <code>implementation</code>.</li>
 	<li>Libraries that are only used in <em>some</em> modules are dependencies only in those modules.</li>
 	<li>The project makes extensive use of data syncing etc too, so there are also <code>base-data</code>, <code>dashboard-data</code> etc modules, following the same logic.</li>
 	<li>The <code>dashboard</code> feature module depends on <code>dashboard-data</code>.</li>
 	<li>The <code>app</code> module depends only on feature modules, <code>dashboard</code>, <code>profile</code>, etc.</li>
</ul>
I strongly suggest sketching out your module dependency flow beforehand, we ended up with ~15 or so modules, all strictly organised. In your case, you mentioned it's already quite a large app, so I imagine <code>app</code> needs feature modules pulled out of it, as does <code>domain</code>. Remember, small modules mean less code recompiled!

We encountered some issues with making sure the same version (<code>buildType</code>, <code>flavors</code>) of the app is used across all submodules. Essentially, all submodules have to have the same <code>flavor</code>s and <code>buildType</code>s defined as the <code>app</code> module.

On the other side of the coin, multi module development does really make you think about dependencies, and enforces strict separation between features. You're likely to run into a few unexpected problems that you've never considered before. For example, something as simple as displaying the app's version <a href="https://blog.jakelee.co.uk/how-to-display-app-version-inside-a-submodule/" rel="noreferrer">suddenly complicates</a>.

<a href="https://medium.freecodecamp.org/how-modularisation-affects-build-time-of-an-android-application-43a984ce9968" rel="noreferrer">This article</a> also helped us decide on our approach. There are other excellent articles around nowadays, which I wish had existed when we'd transitioned!

Here's an example diagram for an app I'm currently working on. Whilst it is far from perfect (the real world unfortunately intrudes!), all of the principles outlined above are visible:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/LBrVD.png"><img class="aligncenter size-large wp-image-2388" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/02/LBrVD-1024x383.png" alt="" width="730" height="273" /></a>

Note that distinguishing between <code>api</code> and <code>implementation</code> would be a good next step for a diagram like this, so that dependencies are more obvious.