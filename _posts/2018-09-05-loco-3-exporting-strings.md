---
ID: 1671
post_title: 'Loco 3: Exporting strings'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/loco-3-exporting-strings/
published: true
post_date: 2018-09-05 10:00:21
---
<a href="https://localise.biz" target="_blank" rel="noopener">Loco</a> is a translation management tool with a staggering array of features, and a very reasonable free plan. <a href="https://blog.jakelee.co.uk//tag/loco/">This series of guides</a> will cover the basics of using Loco, continuing with Part 3: Exporting strings in a variety of formats.

These articles are <b>not</b> sponsored!

<!--more-->

To export strings, a user needs to have the "Access developer tools" permission. The following options and formats are available
<h3>Options <a href="https://localise.biz/help/formats/exporting" target="_blank" rel="noopener">(help)</a></h3>
<img class="alignnone size-full wp-image-1672" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/jnj97ou.png" alt="jnj97ou" width="543" height="672" />
<ol>
 	<li><b>Locale selection</b>: Locale selection allows you to either export a single, multiple or all locales. If multiple are selected, a zip file will be provided with each file inside a folder structure suited for the export format (4).</li>
 	<li><b>Locale fallback</b>: Specifying a default locale allows missing strings to be provided with a default translation, this can be useful when a translation has not been completed yet, but all strings are required to build the project.</li>
 	<li><b>Tag filter</b>: Multiple tags can be used to ensure only the relevant strings are exported. For example, a "Password Reset" module of an app may only need the strings tagged "Password Reset". <a href="https://blog.jakelee.co.uk//2018/08/29/loco-1-string-management-for-multi-platform-multi-locale-projects/" target="_blank" rel="noopener">More info on tags</a>.</li>
 	<li><b>File format</b>: The format of the exported data. This will be covered fully in the next section.</li>
 	<li><b>Index</b>: This specifies what ID should be used for the text. Usually this will be the string name ("Asset ID"), but for some use cases it may be useful to use a different locale's full string.</li>
 	<li><b>Order</b>: By default, strings are ordered by date added, this options provides the ability to order by a string's Asset ID instead.</li>
 	<li><b>Status</b>: Similar to the Tag Filter, this allows only exporting strings that have a specific status / flag, for example only exporting translated strings.</li>
 	<li><b>API Endpoint</b>: The URL to call to export strings at the current settings, this will be covered in more detail in Part 4 of this series.</li>
</ol>
<h3>Formats</h3>
The <a href="https://localise.biz/api#formats" target="_blank" rel="noopener">full list of basic export formats</a> is reproduced below:
<ul>
 	<li><code>.pot .po .mo</code>: Gettext PO, POT and binary MO</li>
 	<li><code>.tmx</code>: Translation Memory eXchange</li>
 	<li><code>.xlf .xliff</code>: Localization Interchange File Format</li>
 	<li><code>.csv</code>: Comma separated values</li>
 	<li><code>.sql</code>: MySQL INSERT statements</li>
 	<li><code>.resx</code>: ResX for .NET framework</li>
 	<li><code>.html</code>: HTML table</li>
 	<li><code>.strings</code>: iOS Localizable strings</li>
 	<li><code>.stringsdict</code>: iOS plural rules</li>
 	<li><code>.plist .bplist</code>: Apple property list XML and binary formats</li>
 	<li><code>.properties</code>: Java properties file</li>
 	<li><code>.ts</code>: An XML format for Qt Framework</li>
</ul>
Additionally, the following formats have multiple options available:
<ul>
 	<li><code>.json</code>: Available in Chrome, Jed, or i18next language pack formats.</li>
 	<li><code>.php</code>: Available in a variety of framework formats, specifically Zend, Symfony (array or INI), Code Igniter, or as constant definitions.</li>
 	<li><code>.xml</code>: Available in Java properties or Tizen format.</li>
 	<li><code>.yml</code>: Available in Simple YAML, Symfony YAML, Ruby on Rails YAML, or a generic nested format.</li>
</ul>
<h3>Notes</h3>
In many agile projects, string management may be ongoing through the product's development, particularly if multiple locales are being used. Exporting a full set of Loco data before each new build ensures any updated translations are automatically included, meaning updating all string is just a couple of clicks away instead of manually editing files!