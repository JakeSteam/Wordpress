---
ID: 1297
post_title: >
  Selectively Playing Tracks Whilst Game
  Is Active / Open
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/selectively-playing-tracks-whilst-game-is-active-open/
published: true
post_date: 2017-06-05 19:29:04
---
<h2>The Problem</h2>
Playing background music on Android is pretty easy: just start a service with a media player. Great, that was easy! However, when the user presses the home button, the music... continues. This is good for music apps, but awful for games. In this example, <a href="https://www.reddit.com/r/BlacksmithSlots/" target="_blank" rel="noopener noreferrer">Blacksmith Slots</a> had one music track for the intro, and one for the main game.<!--more-->
<h2>The Solution</h2>
The basic idea for the solution comes from <a href="https://stackoverflow.com/a/12652213/608312" target="_blank" rel="noopener noreferrer">a 5 year old StackOverflow answer</a>, with the only upvote being mine. Essentially, the music is stopped whenever an activity is paused, unless a boolean flag is set. In this implementation, a <code>MusicHelper</code> singleton was used, so that any activity could change audio track if necessary.
<h4>Media Service</h4>
The media service is a relatively basic service that creates a media player and starts it. The code is <a href="https://gist.github.com/JakeSteam/55cd4384f92b76f8a6f1c82a32e04124#file-musicservice-java" target="_blank" rel="noopener noreferrer">available on this article's Gist</a>, but essentially the track to play is passed via a reference to the asset (e.g. <code>R.raw.music</code>) on the service's start intent.

A reference to the service is stored within the <code>MusicHelper</code>, and assigned by the following:
<pre>
musicService = new Intent(context.getApplicationContext(), MusicService.class)
    .putExtra("songId", trackToPlay);</pre>
<h4>Base Activity</h4>
This article assumes all of the application's activities extend the same base activity. If this isn't the case, the following can be implemented on every activity, but this is obviously more effort.

When an activity is paused (application exited, user receives a call, starting a new activity, etc) if the <code>movingInApp</code> flag isn't set, the music is paused.
<pre>
@Override
public void onPause() {
    super.onPause();
    if (!MusicHelper.getInstance(this).isMovingInApp()) {
        MusicHelper.getInstance(this).stopMusic();
    }
}</pre>
When an activity resumes the <code>movingInApp</code> flag is set to false, so that the next time an activity pauses it can be checked again.
<pre>
@Override
protected void onResume() {
    super.onResume();
    MusicHelper.getInstance(this).setMovingInApp(false);
}</pre>
<h4>Setting Moving Flag</h4>
In order for the <code>isMovingInApp()</code> check to work, every time an activity is closed or opened, <code>movingInApp</code> must be set to true first. As a base activity was used in this example, a generic close method could be used, but the same code must be called just before starting a new activity too.
<pre>
public void close(View v) {
    MusicHelper.getInstance(this).setMovingInApp(true);
    finish();
}</pre>
<h4>Playing Music</h4>
To play music, the <code>playIfPossible()</code> function can be used, passing the asset that should be played. This will play the track if there is currently no audio playing, or another track is currently being played.
<pre>MusicHelper.getInstance(this).playIfPossible(R.raw.village_consort);</pre>
As mentioned before, <code>playIfPossible()</code> checks if the track isn't currently playing, then starts the service if necessary. This is done in a new thread to avoid UI stutters. Note that the project used as an example allows the user to mute music at any time, including before settings have been setup, hence playing if it is intro music, and checking the user's preference. This is of course optional.
<pre>
public void playIfPossible(final int trackToPlay) {
    new Thread(new Runnable() {
        public void run() {
            // If music should be playing, and it isn't, or is the wrong track, fix that!
            if ((isIntroMusic(R.raw.time_passes) || Setting.getBoolean(Enums.Setting.Music)) &amp;&amp;
                    (trackToPlay != currentTrack || !musicServiceIsStarted)) {
                musicService = new Intent(context.getApplicationContext(), MusicService.class)
                    .putExtra("songId", trackToPlay);
                currentTrack = trackToPlay;
                context.startService(musicService);
                musicServiceIsStarted = true;
            } else if (!Setting.getBoolean(Enums.Setting.Music) &amp;amp;&amp;amp; musicServiceIsStarted) {
                context.stopService(musicService);
                musicServiceIsStarted = false;
            }
        }
    }).start();
}</pre>
<h2>The Conclusion</h2>
Background music is absolutely essential in a game, and being able to control this playback is just as important. If a game's music continues when the app is closed, the user is likely to uninstall it pretty quickly, and understandably so.

The implementation described in this article will also stop music when the activity is temporarily paused, and start it again when it is resumed. In most cases this isn't ideal, so the media player's start and stop functions could probably be replaced with play and pause functions, but it was not necessary for this use case.

I'd also like to say thank you to <a href="https://stackoverflow.com/users/1069068/raghav-sood" target="_blank" rel="noopener noreferrer">the user</a> who posted the initial idea, it's a rather elegant solution to a tricky problem. I'm sure in the 5 years since it was posted, there have been libraries and better solutions discovered, but I was unable to find any. Besides, this one works perfectly!

As always, all code used in this article is available as a <a href="https://gist.github.com/JakeSteam/55cd4384f92b76f8a6f1c82a32e04124" target="_blank" rel="noopener">GitHub Gist</a>.