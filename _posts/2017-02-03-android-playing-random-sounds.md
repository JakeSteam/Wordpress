---
ID: 714
post_title: Playing Random Sounds on Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/android-playing-random-sounds/
published: true
post_date: 2017-02-03 12:00:50
---
<h2>The Problem</h2>
During various events in <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener">Pixel Blacksmith</a>, a sound effect needs to play. There is more than one possible sound effect per event, so one needs to be randomly picked each time. <!--more-->
<h2>The Solution</h2>
First, arrays of references to all possible sound files (stored in <code>.ogg</code> / <code>.mp3</code> / <code>.wav</code> format in <code>src/main/res/raw</code>) are created. They can be final and static, since they're never going to change, but need to be public so that the rest of the application can refer to them.
<pre>
public static final int[] enchantingSounds = {R.raw.enchant1};
public static final int[] sellingSounds = {R.raw.sell1, R.raw.sell2};
public static final int[] smithingSounds = {R.raw.smith1, R.raw.smith2, R.raw.smith3};
public static final int[] walkingSounds = {R.raw.footsteps1};
public static final int[] transitionSounds = {R.raw.slide1, R.raw.slide2, R.raw.slide3};</pre>
When a sound-triggering event is played, the relevant reference array is passed to a <code>playSound()</code> function from an activity, using <code>SoundHelper.playSound(this, SoundHelper.transitionSounds)</code>.

The <code>playSound()</code> function selects an item from the array at random by picking a number between zero and the length of the array, then selecting that element of the zero-indexed array. Now that a specific sound file reference has been determined, this is passed to a more specific <code>playSound()</code>.
<pre>
// If an array is passed, pick one at random to play.
public static void playSound(Context context, int[] sounds) {
    int soundID = sounds[new Random().nextInt(sounds.length)];
    playSound(context, soundID);
}</pre>
Now that the specific file has been identified, a check is performed to see if the player has sounds enabled. If they don't, they won't be happy if the sound plays! If they do, a new <code>MediaPlayer</code> is created and started. This is wrapped in a try/catch as I've personally found media players to be somewhat error prone, despite being a core Android building block.
<pre>
private static void playSound(Context context, int soundID) {
    // Only play if the user has sounds enabled.
    if (Setting.getSafeBoolean(Constants.SETTING_SOUNDS)) {
        try {
            MediaPlayer mediaPlayer = MediaPlayer.create(context, soundID);
            mediaPlayer.start();
        } catch (Exception e) {
            Log.d("Blacksmith", e.toString());
        }
    }
}</pre>
<h2>The Conclusion</h2>
This solution works well for quick and simple playing of short sound effects. However, sound effects can overlap, so it would not be suitable for more complex sound effects such as an enemy boss making specific noises per attack. It's very reliable however, and even short sound effects can help add character to the game.

Additionally, a large number of free sound samples are available from <a href="https://www.freesound.org/" target="_blank" rel="noopener">FreeSound</a>, and I highly recommend taking a look if a specific sound sample is wanted.

A full Gist of this solution <a href="https://gist.github.com/JakeSteam/94a0b5bce8396c00eafa4a89c237e834" target="_blank" rel="noopener">is available here</a>.