---
ID: 2488
post_title: 'How to disable your app&#8217;s preview image in Android&#8217;s task / app switcher'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-disable-your-apps-preview-image-in-androids-task-app-switcher/
published: true
post_date: 2019-05-13 15:00:42
---
The easiest way to disable your app's preview when your app is shown in task switcher is <code>FLAG_SECURE</code>.

When this is enabled, your app won't display any previews, and instead shows a blank screen for most devices.

This can be done by creating a base <code>Activity</code> class that all activities extend, containing this in the <code>onCreate</code>:
<pre>protected void onCreate(@Nullable Bundle savedInstanceState) {
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE);
    super.onCreate(savedInstanceState);
}</pre>
Alternatively, you can selectively enable it on pause / resume (although the first approach is better):
<pre>override fun onPause() {
    super.onPause()
    getWindow().setFlags(WindowManager.LayoutParams.FLAG_SECURE, WindowManager.LayoutParams.FLAG_SECURE)
}

override fun onResume() {
    super.onResume()
    getWindow().clearFlags(WindowManager.LayoutParams.FLAG_SECURE)
}</pre>
Originally from <a href="https://stackoverflow.com/a/56043235/608312" target="_blank" rel="noopener noreferrer">my (ultimately unhelpful) StackOverflow answer</a>.