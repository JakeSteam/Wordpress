---
ID: 2568
post_title: Tutorials overview for data binding
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/tutorials-overview-for-data-binding/
published: true
post_date: 2019-07-30 15:00:29
---
Somehow I've spent a few years developing for Android without coming across data binding in a real project. I've seen it mentioned, assumed the basics, but never actually used it. I recently had the opportunity to use some of it's more advanced capabilities for work, which seemed a good time to try out the most popular data binding tutorials!

My method of finding tutorials was very simple, I just Googled "data binding android tutorial" and ran through the top 5! I followed all of the tutorials inside one project (<a href="https://github.com/JakeSteam/data-binding-experiments" target="_blank" rel="noopener noreferrer">available on GitHub</a>), with branches and regular commits for each tutorial. Whilst this post will hopefully help you choose a tutorial, the repository will also give a nice example of the finished product. Tutorials are listed in the order they appeared in my search results.<!--more-->
<h2>"Using data binding in Android" - Lars Vogel</h2>
As with all Vogella tutorials, <a href="https://www.vogella.com/tutorials/AndroidDatabinding/article.html" target="_blank" rel="noopener noreferrer">this lengthy article</a> very much prioritises showing over telling. Whilst this is often a good tactic, in this case it results in any reader just blindly copy and pasting code without actually knowing what it does. For example, step 2.1 - 2.5 are an exercise for the reader that tells them nothing except "run this code". Whilst there is some explanation of (similar) code in step 1, the subtle differences mean this isn't actually very helpful.

The end result is a technically competent implementation of data binding, but with little actual knowledge transferred. As seems to be a running theme in Vogella tutorials, I believe the code was created first, then a tutorial written up as an afterthought. There are a few small typos through the article, but the actual code is nicely formatted etc.

One advantage of this tutorial is it does go slightly beyond the basics, by introducing binding adapters. This nice idea is unfortunately immediately counteracted by the lack of any explanation of what a custom converter is, why you might use one, or what the considerations of use are.

I really wish I could get on better with the tutorial, but just being shown screens of code with little explanation is barely better than a GitHub repo.
<h2>"Android working with DataBinding" - Ravi Tamada</h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/androidhive.png"><img class="alignleft size-medium wp-image-2571" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/androidhive-300x279.png" alt="" width="300" height="279" /></a>As a contrast to the previous tutorial, <a href="https://www.androidhive.info/android-working-with-databinding/" target="_blank" rel="noopener noreferrer">AndroidHive's data binding tutorial</a> explains all code shown. It very clearly explains the concept and reasoning behind data binding, as well as explaining what every block of code does.

Unusually for these tutorials, FAB / button click events were covered in detail. From passing parameters to binding the handler, the common use cases were covered nicely. A nice touch was providing binding adapter instructions for Picasso &amp; Glide, allowing users to apply whatever existing image loading library knowledge they had.

A short but sweet tutorial, it's definitely worth reading to get to grips with a few core data binding concepts.
<h2>"Data Binding for Android Beginners" - Tobiloba Adejumo</h2>
The first distinguishing feature of <a href="https://medium.com/mindorks/learn-android-data-binding-the-easy-way-3f92dd44e5d6" target="_blank" rel="noopener noreferrer">MindOrks' tutorial</a> that immediately jumps out is the ability to listen to it. It turns out to just be text-to-speech, it's a nice idea though!

I like the emphasis on Q&amp;A this article starts with, helping clear up a few concerns before diving into the code. Unfortunately, once this introduction is finished and the bare bones code is explained, the article has ended! Whilst it has some good information inside, the end result is the absolute bare minimum data binding implementation possible.

In the article's defence it does start with "This is the first article in this series". But, with no apparent link to the second article even 6 months later, it's hard to see why it ended up so highly ranked on Google, as it's not a good resource for beginners.
<h2><strong>"Android Data Binding codelab" - Google</strong></h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/codelabs.png"><img class="alignleft size-full wp-image-2572" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/codelabs.png" alt="" width="248" height="691" /></a>As the official data binding resource from Google themselves, I had high hopes for <a href="https://codelabs.developers.google.com/codelabs/android-databinding/#0" target="_blank" rel="noopener noreferrer">their codelab tutorial</a>. High hopes that were lived up to by the extremely high level of quality throughout.

The inclusion of screenshots and helpful tips alongside the code really helps enhance understanding. Additionally, as the code files aren't being shown in their entirety repeatedly, actually implementing the codelab requires reading and understanding the instructions.

Each step also contains links to external resources, allowing readers to dive deeper into specific concepts if they are interested / need more information. All of the most used elements of view binding are covered excellently, from binding adapters to live data. Each only explains the concepts as much as is necessary, supplemented by external links.

The GitHub repo also makes the unusual decision to provide duplicates of the files worked upon, one for each stage of the tutorial. This allows the developer to easily check what their files should look like in case there is any confusion.

Having the tutorial exclusively in Kotlin is excellent for those of us who mostly work in it, but it would have been nice if there was a Java version too, just like the official Android developer documentation. Perhaps my favourite part of this tutorial is the links to additional samples and further documentation at the very end, encouraging the developer to continue learning.

From the codelabs' clean interface to the engaging writing, this tutorial is by far the highest quality covered in this post. I'd absolutely recommend running through it, even if just to look at the useful resources linked.
<h2>"Data Binding in Android" by Morris</h2>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/TqeKhEC.png"><img class="alignleft wp-image-2570 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/07/TqeKhEC-286x300.png" alt="" width="286" height="300" /></a><a href="https://androidwave.com/data-binding-in-android-tutorial/" target="_blank" rel="noopener noreferrer">This tutorial by AndroidWave</a> made the decision to produce a fully designed prototype to databind, instead of the usual proof of concept style. This has the significant disadvantage of the first block of code being 150 lines of XML. Almost all of it completely irrelevant to databinding (and containing typos).

It also has dependencies on drawables, colours, and even custom fonts that need to be implemented, in the first file alone! This same giant block of XML is shown multiple times in the article, with the differences not made clear.

The quality of the writing is unfortunately relatively low too, with a few sentences requiring a few reads to be understood. As a counterpoint to this though, the end result really is nicely designed!

&nbsp;