---
ID: 867
post_title: 'Converting Levels Into XP &#038; Vice Versa'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/converting-levels-into-xp-vice-versa/
published: true
post_date: 2017-03-01 12:07:29
---
<h2>The Problem</h2>
Many games (such as my own <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener noreferrer">Pixel Blacksmith</a> and <a href="https://www.reddit.com/r/BlacksmithSlots/" target="_blank" rel="noopener noreferrer">Blacksmith Slots</a>) contain an XP / level system, where performing actions will reward experience, and eventually new levels. These new levels often unlock new content, or provide currency, so keeping players incentivised without feeling like a "grind" is a tricky balance.<!--more-->
<h2>The Solution</h2>
First, come up with a basic formula to use for calculating the XP required for a level. The most common by far is <code>(level/x)^y</code>, with <code>x</code> affecting the amount of XP (lower values = more XP required per level), and <code>y</code> being how quickly the required xp per level should increase (higher values = larger gaps between levels).

Next, make a spreadsheet where <code>x</code> and <code>y</code> can be quickly and easily modified, and the XP per level seen. <a href="https://docs.google.com/spreadsheets/d/1uFed4cKE1BxxZ19BKuAbbo7Gk6_ezCDmFMV5fwCCxqw/" target="_blank" rel="noopener noreferrer">Here is my spreadsheet</a>, feel free to make a copy.

As an example, here's a comparison of the XP required for levels using varying values of <code>x</code> and <code>y</code>. Note that using 2/3 for <code>y</code> is recommended, so that built in squaring / cubing functions can be used.
<h3>Example 1: X: 0.07, Y: 2</h3>
This has a fairly quickly increasing amount of XP per level (<code>y</code>) and a large amount of XP (<code>x</code>), and is good for games where XP is relatively easy to gain.
<table dir="ltr" border="1" cellspacing="0" cellpadding="0"><colgroup> <col width="60" /> <col width="90" /> <col width="54" /></colgroup>
<tbody>
<tr>
<td>Level</td>
<td>XP</td>
<td>Difference</td>
</tr>
<tr>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>204.0816327</td>
<td>204</td>
</tr>
<tr>
<td>2</td>
<td>816.3265306</td>
<td>612</td>
</tr>
<tr>
<td>3</td>
<td>1836.734694</td>
<td>1020</td>
</tr>
<tr>
<td>4</td>
<td>3265.306122</td>
<td>1429</td>
</tr>
<tr>
<td>5</td>
<td>5102.040816</td>
<td>1837</td>
</tr>
<tr>
<td>6</td>
<td>7346.938776</td>
<td>2245</td>
</tr>
<tr>
<td>7</td>
<td>10000</td>
<td>2653</td>
</tr>
<tr>
<td>8</td>
<td>13061.22449</td>
<td>3061</td>
</tr>
<tr>
<td>9</td>
<td>16530.61224</td>
<td>3469</td>
</tr>
<tr>
<td>10</td>
<td>20408.16327</td>
<td>3878</td>
</tr>
</tbody>
</table>
<h3>Example 2: X: 0.3, Y: 2</h3>
This has a high value for <code>x</code>, so the amounts of XP required for a level up are very small. This would be good for a game where XP is relatively hard to obtain (e.g. requires collecting items). Note that the % increase in XP between levels 1 and 2 is the same as Example 1 (3x higher).
<table dir="ltr" border="1" cellspacing="0" cellpadding="0"><colgroup> <col width="60" /> <col width="90" /> <col width="54" /></colgroup>
<tbody>
<tr>
<td>Level</td>
<td>XP</td>
<td>Difference</td>
</tr>
<tr>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>11.11111111</td>
<td>11</td>
</tr>
<tr>
<td>2</td>
<td>44.44444444</td>
<td>33</td>
</tr>
<tr>
<td>3</td>
<td>100</td>
<td>56</td>
</tr>
<tr>
<td>4</td>
<td>177.7777778</td>
<td>78</td>
</tr>
<tr>
<td>5</td>
<td>277.7777778</td>
<td>100</td>
</tr>
<tr>
<td>6</td>
<td>400</td>
<td>122</td>
</tr>
<tr>
<td>7</td>
<td>544.4444444</td>
<td>144</td>
</tr>
<tr>
<td>8</td>
<td>711.1111111</td>
<td>167</td>
</tr>
<tr>
<td>9</td>
<td>900</td>
<td>189</td>
</tr>
<tr>
<td>10</td>
<td>1111.111111</td>
<td>211</td>
</tr>
</tbody>
</table>
<h3>Example 3: X: 0.07, Y: 3</h3>
This uses a different value for <code>y</code> than the other 2 examples, and as such the XP required rapidly increases. This would be used in a game where higher levels have significantly higher XP gaining potential (e.g. an incremental game).
<table dir="ltr" border="1" cellspacing="0" cellpadding="0"><colgroup> <col width="60" /> <col width="90" /> <col width="54" /></colgroup>
<tbody>
<tr>
<td>Level</td>
<td>XP</td>
<td>Difference</td>
</tr>
<tr>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>1</td>
<td>2915.451895</td>
<td>2915</td>
</tr>
<tr>
<td>2</td>
<td>23323.61516</td>
<td>20408</td>
</tr>
<tr>
<td>3</td>
<td>78717.20117</td>
<td>55394</td>
</tr>
<tr>
<td>4</td>
<td>186588.9213</td>
<td>107872</td>
</tr>
<tr>
<td>5</td>
<td>364431.4869</td>
<td>177843</td>
</tr>
<tr>
<td>6</td>
<td>629737.6093</td>
<td>265306</td>
</tr>
<tr>
<td>7</td>
<td>1000000</td>
<td>370262</td>
</tr>
<tr>
<td>8</td>
<td>1492711.37</td>
<td>492711</td>
</tr>
<tr>
<td>9</td>
<td>2125364.431</td>
<td>632653</td>
</tr>
<tr>
<td>10</td>
<td>2915451.895</td>
<td>790087</td>
</tr>
</tbody>
</table>
<h3>Using The Formula</h3>
Now that we have a formula for XP required for any level (using Example 1: <code>XP = (level/0.07)^2</code>), we also need to know the current level based on XP. Inverting the formula gives us <code>level = 0.07 * √XP</code>.  The second formula can be harder to implement due to requiring "nth root of", hence why using a value of 2/3 is recommended.

XP and levels can now be converted easily and efficiently, without requiring any lengthy formulas. However, how useful the formula will be depends entirely on how well the <code>x</code> and <code>y</code> values are tuned for the specific use case. Get it wrong, and players will power through content or complain about being too grindy, get it right and they'll never mention it!
<h2>The Conclusion</h2>
Levels are an extremely common way of rewarding consistent players, and providing an incentive to continue playing the game, or to replay older content. Balancing the formula is extremely important, and the simplicity of the formula described in this post means that consistent complaints of level ups being too frequent or too infrequent can be fixed by changing a single variable.

It is applicable to pretty much any game on any platform, but is especially useful in mobile applications, where processing power can be more limited, and player attention harder to keep.

A gist of a Java implementation of converting between XP &amp; levels, as well as calculating current % progress towards next level is <a href="https://gist.github.com/JakeSteam/4d843cc69dff4275acd742b70d4523b6" target="_blank" rel="noopener noreferrer">available here</a>.