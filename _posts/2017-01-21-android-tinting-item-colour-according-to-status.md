---
ID: 545
post_title: 'Android: Tinting Item Colour According To Status'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/android-tinting-item-colour-according-to-status/
published: true
post_date: 2017-01-21 01:24:00
---
<h2>The Problem</h2>
<a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener">Pixel Blacksmith</a> is an Android game where players craft and sell items to visitors, in order to make a profit to buy upgrades / more resources. These items can be enchanted with gems, and the item's image needs to be tinted the gem's colour to reflect this.

<!--more-->
<h2>The Solution</h2>
To modify the item's colour (whilst only affecting the transparent pixels), we'll be using the <code>MULTIPLY</code> color filter option. It can be hard to predict the outcome a specific colour code will have when applied to the drawables, so it's recommended to try out 5-6 and choose one which has the desired effect.
<h4>Defining Colours</h4>
First, the hex codes of the desired colours were found via trial and error, and saved in the <code>colors.xml</code> file.
<pre>
&lt;color name="redOverlay"&gt;#ff7777&lt;/color&gt;
&lt;color name="blueOverlay"&gt;#9cb4fc&lt;/color&gt;
&lt;color name="greenOverlay"&gt;#8abc84&lt;/color&gt;
&lt;color name="whiteOverlay"&gt;#e2e2e2&lt;/color&gt;
&lt;color name="blackOverlay"&gt;#3d3d3d&lt;/color&gt;
&lt;color name="purpleOverlay"&gt;#b041ba&lt;/color&gt;
&lt;color name="yellowOverlay"&gt;#e2df0b&lt;/color&gt;</pre>
<h4>Applying Colours</h4>
For (albeit extremely minor, possibly non-existent) performance reasons, a switch statement is used instead of a large if / else. The item's state (i.e. the colour it needs tinting) is checked against the list of states, and the correct colour is applied. The <code>MULTIPLY</code> porterduff mode is used. The technicalities of this aren't necessary to know, but <a href="https://i.imgur.com/62EDoqI.png" target="_blank" rel="noopener">a reference image</a> is very useful (source: <a href="https://softwyer.wordpress.com/2012/01/21/1009/" target="_blank" rel="noopener">Softwyer</a>). Essentially, multiple merges the two resources, in this case the item image and the colour overlay, but only the pixels where the first resource isn't fully transparent.

<code>imageResource</code> is the item drawable to be modified. If no coloured state is identified, any existing colour filters are cleared, in case they've somehow got attached erroneously earlier on.
<pre>
            switch (itemState) {
                case Constants.STATE_RED: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.redOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_BLUE: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.blueOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_GREEN: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.greenOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_WHITE: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.whiteOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_BLACK: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.blackOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_PURPLE: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.purpleOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                case Constants.STATE_YELLOW: imageResource.setColorFilter(ContextCompat.getColor(context, R.color.yellowOverlay), PorterDuff.Mode.MULTIPLY);
                    break;
                default: imageResource.clearColorFilter();
                    break;
            }</pre>
Note that <code>ContextCompat.getColor(context, id)</code> is used to get the resolved colour instead of the very common, but deprecated, <code>context.getResources().getColor(id)</code>. The latter method is extremely widespread, so unlikely to ever actually be removed, but it's still always worth avoiding deprecated methods, to cut down on future maintenance.
<h2>The Conclusion</h2>
Whilst providing a visual representation of the item's state is a minor UI change, it helps reinforce the differences between items, and make the game's items seem more varied. Identifying the colour filter mode and colour codes to use are rather dull exercises in trial and error, but the final effect is very effective.

Tinting a drawable with a colour is a common requirement in apps, helping to cut down on the number of unique drawables needed. This is much easier if the drawable is a single colour, specifically white. Below, the difference in effect between an ideal item (mostly white) and an imperfect one (2 strong colours, green and gold). The effect is much more obvious in the first, although still noticeable in the second.

[gallery ids="589,590" type="rectangular" link="file"]