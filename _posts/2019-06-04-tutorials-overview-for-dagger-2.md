---
ID: 2517
post_title: Tutorials overview for Dagger 2
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/tutorials-overview-for-dagger-2/
published: true
post_date: 2019-06-04 14:47:20
---
Whilst I've used the dependency injector <a href="https://github.com/google/dagger" target="_blank" rel="noopener noreferrer">Dagger 2</a> a few times before, my knowledge was very much gained "in the field", i.e. from seeing it in the wild. I recently decided to learn how to use it properly, so worked through a few of the more popular tutorials inside a repo. <a href="https://github.com/JakeSteam/dagger-experiments" target="_blank" rel="noopener noreferrer">This GitHub repo is available here</a>, and contains completed versions of all of the tutorials listed here.

In this post I'll briefly cover the 5 tutorial series used. They're all very high quality, and I'm extremely grateful to the authors for helping improve my understanding!

PS: The<a href="https://discord.gg/2BgNxhC" target="_blank" rel="noopener noreferrer"> /r/AndroidDev Discord server</a> has a <code>#dependency-injection</code> channel if you need any Dagger 2 help.
<!--more-->
<h2>"Dagger 2 for Dummies in Kotlin" - Elye</h2>
This series by <a href="https://medium.com/@elye.project" target="_blank" rel="noopener noreferrer">Elye</a> was my personal favourite mentioned in this post. It ramped the difficulty up very gently, keeping all implementations as simple as possible to avoid confusion. The writing style is very informal, the memes / typos of which can be a little distracting, but the clear content outweighs this.

All of the posts contain plenty of diagrams, which really aid understanding of the more abstract concepts of Dagger. Intuitive names (e.g. "MagicBox") are used, along with notes on the more common naming standards.

It comes in 6 parts (<a href="https://medium.com/@elye.project/dagger-2-for-dummies-in-kotlin-with-one-page-simple-code-project-618a5f9f2fe8" target="_blank" rel="noopener noreferrer">Intro</a>, <a href="https://medium.com/@elye.project/dagger-2-for-dummies-in-kotlin-provides-and-module-b84dca1b0d03" target="_blank" rel="noopener noreferrer">Provides &amp; Module</a>, <a href="https://medium.com/@elye.project/dagger-2-for-dummies-qualifier-5c7e78a4d3a6" target="_blank" rel="noopener noreferrer">Qualifier</a>, <a href="https://medium.com/@elye.project/dagger-2-for-dummies-in-kotlin-testing-ab9af2a69bad" target="_blank" rel="noopener noreferrer">Testing</a>, <a href="https://medium.com/@elye.project/dagger-2-for-dummies-in-kotlin-scope-d51a6b6e077f" target="_blank" rel="noopener noreferrer">Scope</a>, <a href="https://medium.com/@elye.project/dagger-2-for-dummies-in-kotlin-subcomponent-5a969b6aec7a" target="_blank" rel="noopener noreferrer">Subcomponent</a>), each of which have a repo at the end.
<h2>"An Absolute Beginner's Tutorial on ... Dependency Injection" - Rahul Rajat Singh</h2>
This tutorial from 2013 is not Android, or even Java specific. It instead introduces the concepts of dependency inversion, inversion of control, and dependency injection, essential to Dagger. Whilst it is written in C#, the concepts are all simple enough that rewriting them in Kotlin was not a particularly tricky barrier. Admittedly, without having prior C# knowledge this would have been more challenging, but the extremely high quality of the writing convinced me to work through it.

All of the concepts are worked through completely, with the surrounding logic / concepts described in a very straightforward manner. The example used throughout is to do with logging system messages.

<a href="https://www.codeproject.com/Articles/615139/An-Absolute-Beginners-Tutorial-on-Dependency-Inver" target="_blank" rel="noopener noreferrer">The post</a> has been rewritten into Kotlin within <a href="https://github.com/JakeSteam/dagger-experiments" target="_blank" rel="noopener noreferrer">my implementation repo</a>, which may be helpful when following along.
<h2>"Introduction to Dagger 2" - Donato Rimenti</h2>
This single-page tutorial is very short and simple, and effectively shows all of the core Dagger concepts. However, this conciseness and text-only style risks alienating some devs who aren't already familiar with the concepts within.

<a href="https://www.baeldung.com/dagger-2" target="_blank" rel="noopener noreferrer">The post</a> is written in Java, and uses a intuitive "Car with an Engine and Brand" example.
<h2>"Dagger 2 Tutorial: Dependency Injection Made Easy" - Jose Manuel Garcia Maestre</h2>
<a href="https://dzone.com/articles/dagger-2-tutorial-dependency-injection-made-easy" target="_blank" rel="noopener noreferrer">This tutorial</a> is another single-page one, that provides the minimum knowledge needed to get started with Dagger. Unfortunately this tutorial doesn't dive very deep, and uses an unnecessarily confusing example (injecting blood types into a body). Still, it provides the reader with the essentials, and would be useful as a refresher article.
<h2>"How-to Dagger 2 with Android" - John Tucker</h2>
This 2 part tutorial (<a href="https://proandroiddev.com/how-to-dagger-2-with-android-part-1-18b5b941453f" target="_blank" rel="noopener noreferrer">part 1</a>, <a href="https://proandroiddev.com/how-to-dagger-2-with-android-part-2-10f4fb8f62d0" target="_blank" rel="noopener noreferrer">part 2</a>) suffers a little bit from jumping around many files, risking the reader losing track of what the core concepts actually are, and getting caught up in boilerplate code. The posts are very code heavy, which can be beneficial for some readers, but personally I would have preferred more explanation along the way.