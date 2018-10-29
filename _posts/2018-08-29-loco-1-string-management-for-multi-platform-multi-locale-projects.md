---
ID: 1651
post_title: 'Loco 1: String management for multi-platform &#038; multi-locale projects'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/loco-1-string-management-for-multi-platform-multi-locale-projects/
published: true
post_date: 2018-08-29 20:50:32
---
<a href="https://localise.biz" target="_blank">Loco</a> is a translation management tool with a staggering array of features, and a very reasonable free plan. <a href="https://blog.jakelee.co.uk//tag/loco/">This series of guides</a> will cover the basics of using Loco, starting with Part 1: Creating a project and adding strings.

These articles are <b>not</b> sponsored!

<!--more-->

<h3>Creating an account</h3>

First, create an account with either your email, or using GitHub, Twitter, or Facebook login. Once your email has been confirmed / permission has been given, a super simple signup screen asks for a password and not much more.
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/nde6pf6.png" alt="nde6pf6" width="989" height="558" class="alignnone size-full wp-image-1652" />

<h3>Creating a project</h3>

A default project will be created for you upon signup, with customisable URL, name, and description. The URL for your project will be the very simple <code>https://localise.biz/username/project</code>.

<h3>Adding locales</h3>

Next, add the locales / languages the strings should be translated into via the <code>+</code> button next to your default locale in the top left of the "Manage" tab. Locales can be anything from a simple language (e.g. French) into a fully custom identifier. A particularly useful feature is the ability to rename locales, which can be extremely useful when the same text needs to be translated into the same language multiple times (e.g. different brands within the same country).
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/kqiptna.png" alt="kqiptna" width="529" height="512" class="alignnone size-full wp-image-1653" />

<h3>Adding strings</h3>

It's finally time to start actually adding text to translate, or "Assets". Click "New asset" and a popup will appear which can be a little overwhelming!

<ol>
<li><b>Source text</b> is the initial text to use, in the project's native locale (e.g. English).</li>
<li><a href="https://localise.biz/help/glossary/asset-id" target="_blank"><b>Asset ID</b></a> is the name used to reference the text in code. This will be something like <code>Dialog_Title_IncorrectPassword</code> or <code>dialog-title-incorrectpassword</code> depending on your preferred style.</li>
<li><a href="https://localise.biz/help/glossary/asset-context" target="_blank"><b>Context</b></a> is used when a text's use is ambiguous, but is generally not needed for most use cases due to Notes.</li>
<li><b>Type</b> should almost always be "Plain text", unless HTML is needed.</li>
<li><b>Tags</b> are a powerful way of categorising strings, as each string can have one or more. A common use case is adding a tag per screen (e.g. "Login", "Registration"), so that all the strings for a specific screen can easily be viewed at once. Shared strings will have multiple screen tags.</li>
<li><b>Notes</b> are a way to provide extra information to translators, generally used if the Asset ID and Tags by themselves are not descriptive enough.</li>
</ol>

<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/0fv3giy.png" alt="0fv3giy" width="546" height="621" class="alignnone size-full wp-image-1654" />

<h3>String statuses</h3>

Once a string has been added, it will by default have the status of "Translated" in the original locale, and "Untranslated" in all other locales. These statuses can be changed at will, and feature 5 different "in progress" states to help collaboration between different project teams. For example, in a recent project new strings were marked as "Provisional" when they had been translated by developers, "Unapproved" when a professional translator had corrected mistakes, and "Translated" when a line manager had signed off a translation. 
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/dchlb7s.png" alt="dchlb7s" width="537" height="342" class="alignnone size-full wp-image-1655" />

<h3>Plan pricing</h3>

Loco's free plan is usually generous enough for almost all use cases, even for large projects with a few hundred assets, multiple languages, and many translators. At my company we went for the "Pro" plan, as we needed custom roles for certain individuals. This cost us a grand total of just £4.95/month (approximately $6.50)! 
<img src="https://blog.jakelee.co.uk//wp-content/uploads/2018/08/nf6ilvp.png" alt="nf6ilvp" width="973" height="440" class="alignnone size-full wp-image-1657" />
More information on these quotas are <a href="https://localise.biz/help/accounts/quotas" target="_blank">available on Loco's help pages</a>, and a brief explanation is below. It's worth emphasising that only the project owner needs to pay, and all other project collaborators can benefit.

<ol>
<li><b>Translations</b> refers to the total number of translations used across the entire account. The default limit of 2,000 is equal to a project with 500 strings translated into 3 languages (plus original language). A relatively large app will likely have between 300-600 assets, keeping in mind common text (e.g. "OK") can share the same asset.</li>
<li><b>Collaboration</b> refers to the roles that invited users can have. This will be covered in-depth in a later post, but essentially the free plan allows unlimited translators (can only edit translations), but no additional roles.</li>
<li><b>Projects</b> unsurprisingly refers to the number of projects that can be added to an account.</li>
<li><b>Translatable assets</b> refers to the number of assets that can be included in a single project. The free plan's limits of 2 projects and 1,000 assets per project give the total of 2,000 available translations.</li>
<li><b>Languages</b> refers to the number of different locales / languages that can be added to a project.</li>
<li><b>Revision history</b> refers to the ability to see an asset's history (e.g. who changed status, text, etc). Whilst the free plan's "see previous version" is useful, being able to view a full history is essential for larger projects.</li>
<li><b>Extra translations</b> refers to the ability to purchase more translations if the very generous limits are reached.
</ol>

<h3>Additional features</h3>

Individual strings can also have plurals and placeholders (e.g. <code>%1$s</code> for Android) configured, all changes logged, and comments with user tagging support.

In addition to Loco's core features, a few free utilities are provided such as <a href="https://localise.biz/free/converter/ios-to-android" target="_blank">iOS  Android string conversion</a>, or <a href="https://localise.biz/free/converter" target="_blank">conversion between all common formats</a>.

<a href="https://localise.biz" target="_blank">Loco</a> has such a massive set of features that only the very basics have been covered here. Collaboration, exporting, and command line interface will all be covered in later articles.