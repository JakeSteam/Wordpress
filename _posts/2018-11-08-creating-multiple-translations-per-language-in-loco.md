---
ID: 1975
post_title: >
  Creating Multiple Translations Per
  Language In Loco
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-multiple-translations-per-language-in-loco/
published: true
post_date: 2018-11-08 19:21:19
---
For most translation projects, the goal is to translate the base language to other dialects. However, if you are creating an app with multiple variants, translations can also be used to localise the project to specific audiences. For example, a collection of fast food apps may have different text for Pizza, Indian, or Chinese food.

Managing these text sets can be tricky, but using a translation management tool like Loco helps keep all the variants under control.

<!--more-->

Whilst Loco isn't designed for managing multiple translations in a single language, it can be done. The secret is using a custom country code as your language tag, but this comes with quite a few complications.

Alternative approaches, such as picking an arbitrary other language to represent a strings variant, have the massive disadvantage of confusing any future internationalisation efforts.
<h2>Creating a new variant</h2>
To add a locale, first press "New locale" on Loco's sidebar. This will display the locale adding dialog, with "Basic", "Custom", and "Advanced" options.
Select "Advanced". Now, enter the locale's unique identifier. This will be covered further in the next section, but it should be your 2 letter language code, followed by a "-" and up to 8 lowercase characters.
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/addlocale.png"><img class="aligncenter size-full wp-image-1976" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/addlocale.png" alt="" width="522" height="336" /></a>
After pressing "Add locale", you'll see a confirmation popup and your new locale has been created!
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/localeadded.png"><img class="aligncenter size-full wp-image-1977" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/localeadded.png" alt="" width="523" height="72" /></a>
<h2>Language tag limitations</h2>
Loco has 3 different options for subtags (the text in a language tag after the language code) with slightly different rules. <a href="https://localise.biz/help/developers/locales" target="_blank" rel="noopener">Loco's documentation</a> provides more information on these.
<ul>
 	<li>Script: Must start with an uppercase letter, and be 2 or 4 letters long.</li>
 	<li>Variant: Must start with a lowercase letter, and be 2-8 letters long.</li>
 	<li>Extension: Must start with "x-", then 1-8 letters / numbers long.</li>
</ul>
Mousing over a locale in the sidebar and clicking the gear icon will let you view the current configuration, including the "Custom language tag" (under the "Developer" tab).

The "Optional subtags" section will also show you which of the above subtags is currently being used. Note that the language tag cannot be modified by changing these options, it will report a conflict with existing locales. Instead, use the "Developer" tab to do it manually. Additionally, a warning about non-standard subtags will be shown, this is a side-effect of skipping normal locale codes!
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/subtags.png"><img class="aligncenter size-full wp-image-1978" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/subtags.png" alt="" width="520" height="134" /></a>
<h2>Warnings</h2>
Unfortunately this approach comes with a few drawbacks:

Firstly, changing the country's language tag cannot be done via the subtags menu. Instead, it must be done via the "Custom language tag" under the "Developer" tab of the locale settings. Luckily, it's unlikely you'll need to do this very often, if ever.

Secondly, some export formats (specifically Android's <span style="font-family: 'courier new', courier, monospace;">strings.xml</span>) don't properly handle multiple custom locales. Whilst SQL, JSON, HTML, iOS' <span style="font-family: 'courier new', courier, monospace;">Localizable.strings</span> and all others tested correctly include all locales, the Android export compresses them all into a single locale. This can be worked around by exporting them individually, but is a concern to be aware of.