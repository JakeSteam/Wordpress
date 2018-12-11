---
ID: 762
post_title: >
  Calculating Weapon Damage Using Ideal
  Ranges
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/calculating-weapon-damage-with-ideal-ranges/
published: true
post_date: 2017-02-19 14:08:09
---
<h2>The Problem</h2>
Whilst building a turn based strategy shooter, weapons needed to have "ideal ranges". For example, a melee weapon should only work from 1 tile away, a shotgun should prefer short ranges, a sniper rifle should prefer long ranges, etc. The non-melee weapons should still work outside of their ideal range, but with reduced damage. <!--more-->
<h2>The Solution</h2>
First, the weapon ranges must be calculated, then whether the target distance is inside this range or not, and finally calculate the total damage based on the previous value.

These are the minimum and maximum ranges for each type of weapon, along with how numbers are included in this range (note that this is an inclusive range, so includes the minimum and maximum values):
<table dir="ltr" border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td> <strong>Weapon Type</strong></td>
<td><strong>Minimum</strong></td>
<td><strong>Maximum</strong></td>
<td><strong>Range</strong></td>
</tr>
<tr>
<td>Melee</td>
<td>1</td>
<td>1</td>
<td>1</td>
</tr>
<tr>
<td>Short</td>
<td>2</td>
<td>4</td>
<td>3</td>
</tr>
<tr>
<td>Medium</td>
<td>5</td>
<td>7</td>
<td>3</td>
</tr>
<tr>
<td>Long</td>
<td>8</td>
<td>15</td>
<td>8</td>
</tr>
</tbody>
</table>
For this post a consistent set of test values will be used: 1, 2, 3, 5, 8, and 10 tiles away, providing a range of values that fall in and out of all weapon's ideal ranges.
<h3>Tiles Out Of Range</h3>
First, if our test value is within a weapon's ideal range, then it is 0 outside of ideal range. Alternatively, get the minimum absolute value of the difference between the value and the minimum and maximum bounds of the range. That was a bit of a mouthful, an example should clarify!

In this example the long weapon type (range 8-15 tiles) will be firing at a target 5 tiles away. 5 is -3 away from the minimum, 8, and -10 away from the maximum, 15. The absolute value of these two is 3 and 10, the minimum of which is 3. Via this rather convoluted process, the fact that the target is 3 tiles out of range is now known.

The below table shows a full matrix of tiles out of range for each weapon type. The example above is shown in red.
<table style="border-collapse: collapse; width: 641px; height: 214px;" border="0" width="448" cellspacing="0" cellpadding="0">
<tbody>
<tr style="height: 15.75pt;">
<td style="height: 15.75pt; width: 48pt;" width="64" height="21"></td>
<td style="width: 48pt;" align="right" width="64">1</td>
<td style="width: 48pt;" align="right" width="64">2</td>
<td style="width: 48pt;" align="right" width="64">3</td>
<td style="width: 48pt;" align="right" width="64">5</td>
<td style="width: 48pt;" align="right" width="64">8</td>
<td style="width: 48pt;" align="right" width="64">10</td>
</tr>
<tr style="height: 16.5pt;">
<td style="height: 16.5pt;" height="22">Melee</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">2</td>
<td align="right">4</td>
<td align="right">7</td>
<td align="right">9</td>
</tr>
<tr style="height: 16.5pt;">
<td style="height: 16.5pt;" height="22">Short</td>
<td align="right">1</td>
<td align="right">0</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">4</td>
<td align="right">6</td>
</tr>
<tr style="height: 16.5pt;">
<td style="height: 16.5pt;" height="22">Medium</td>
<td align="right">4</td>
<td align="right">3</td>
<td align="right">2</td>
<td align="right">0</td>
<td align="right">1</td>
<td align="right">3</td>
</tr>
<tr style="height: 16.5pt;">
<td style="height: 16.5pt;" height="22">Long</td>
<td align="right">7</td>
<td align="right">6</td>
<td align="right"><span style="color: #ff0000;">5</span></td>
<td align="right">3</td>
<td align="right">0</td>
<td align="right">0</td>
</tr>
</tbody>
</table>
<h3>Damage Multiplier</h3>
Next, the damage multiplier will be calculated, based on how far out of ideal range the target is. This is a simpler formula than before, being <code>1 - (tilesOutOfRange / weaponRange)</code>. Using the same example situation as before, the calculation will be <code>1 - (5 / 8) = 0.375</code>. If a negative damage multiplier is calculated, then 0 will be used instead.

As before, the below table shows a full matrix of damage multiplier for each weapon type based on distance away, with the example highlighted in red. Note that values are rounded to 2 decimal places for visual clarity, so the example becomes 0.38.
<table dir="ltr" border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td></td>
<td><strong>1</strong></td>
<td><strong>2</strong></td>
<td><strong>3</strong></td>
<td><strong>5</strong></td>
<td><strong>8</strong></td>
<td><strong>10</strong></td>
</tr>
<tr>
<td> Melee</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td> Short</td>
<td>0.67</td>
<td>1</td>
<td>1</td>
<td>0.67</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td> Medium</td>
<td>0</td>
<td>0</td>
<td>0.33</td>
<td>1</td>
<td>0.67</td>
<td>0</td>
</tr>
<tr>
<td> Long</td>
<td>0.13</td>
<td>0.25</td>
<td><span style="color: #ff0000;">0.38</span></td>
<td>0.63</td>
<td>1</td>
<td>1</td>
</tr>
</tbody>
</table>
<h3>Damage</h3>
Now, the final step! Simply multiply the weapon's base damage by the multiplier to calculate the actual weapon damage dealt. For such a basic calculation no table is needed, but if the sample long distance weapon is assumed to deal 100 damage, we end up with the conclusion that the weapon will deal 38 damage to a target 5 tiles away. Melee weapons are also only effective 1 tile away.
<h3>Misc</h3>
Below I've quoted my rough notes for the formula, as a more concise but less clear version of the calculations above:
<blockquote><strong>Weapon Range</strong> = (Maximum - Minimum) + 1.
<strong>Tiles Out Of Range</strong> = If in range, then 0. Otherwise, the minimum (absolute) distance between the tile and the minimum or maximum.
<strong>Damage Multiplier</strong> = (1 - (Tiles Out Of Range / Weapon Range).
<strong>Damage</strong> = If damage multiplier &gt; 0, then base damage * damage multiplier, otherwise 0.</blockquote>
Additionally, a Java implementation is available as <a href="https://gist.github.com/JakeSteam/ffc0bef0977e3c9709d3202fbeb803bf" target="_blank" rel="noopener">a Gist</a>, which may be easier to understand.
<h2>The Conclusion</h2>
Whilst the actual use case is fairly niche, this post should indicate why a generic formula is often the preferred approach instead of hardcoding lookup tables. With the approach taken, adding a new weapon type or dynamically adjusting a weapon's range is very easy, and the damage is quickly calculated.

The formula may be useful for anyone needing to calculate values that need to drop off outside of a specific range at a varying rate, based on the size of the range.

Additionally, this was a "iteratively created" formula, where I essentially <a href="https://i.imgur.com/QO62X0P.png" target="_blank" rel="noopener">played with variables</a> in Excel / Google Sheets until I got values that matched what I wanted to see. As such, I strongly suspect some steps are inefficient, particularly when calculating how far out of range a target is.