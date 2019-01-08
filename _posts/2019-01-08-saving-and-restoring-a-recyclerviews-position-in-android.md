---
ID: 2330
post_title: 'Saving and restoring a RecyclerView&#8217;s position in Android'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/saving-and-restoring-a-recyclerviews-position-in-android/
published: true
post_date: 2019-01-08 18:00:37
---
When using a RecyclerView in your Android app, especially one with many (or infinite) items inside it, your users will get pretty frustrated if you fail to remember their position.

I recently used a RecyclerView to show a few hundred rows, each of which may link to another fragment, returning from which will reinitialise the fragment. With the default implementation, every time the RecyclerView's Adapter is assigned the position is reset. In this tutorial, I'll cover how to save, load, and reset this position.

Note that this tutorial will only work if your RecyclerView's content / order won't change. Kotlin is used for this article's code, but the technique also works in Java. It is also <a href="https://gist.github.com/JakeSteam/ce074069c98deb764b9d74e596b87a69" target="_blank" rel="noopener">available as a GitHub Gist</a> if you'd like to just see the code.

<!--more-->
<h2>Prerequisities</h2>
Since the position restoring framework will be reused, first create an interface <code>ListPositioner.kt</code>:
<pre>interface ListPositioner {
    val recyclerScrollKey: String

    fun loadListPosition()

    fun saveListPosition()

    fun resetListPosition()
}</pre>
Next, make sure your controller class (Fragment, Activity, etc) implements <code>ListPositioner</code> (e.g. <code>MyFragment : ListPositioner</code>).

Finally, we need a <code>SharedPreferences</code> key to store the scroll position in:
<pre>override val recyclerScrollKey = "uk.co.jakelee.scrollposition"</pre>
<h2>Saving the RecyclerView's scroll position</h2>
To save the position, we're just going to save the first fully visible item's position into <code>SharedPreferences</code>. First retrieve the scroll position of your <code>RecyclerView</code> (<code>myRecycler</code> in this example), then save it:
<pre>override fun saveListPosition() {
    val position = (myRecycler.layoutManager as LinearLayoutManager).findFirstCompletelyVisibleItemPosition()
    PreferenceManager.getDefaultSharedPreferences(activity!!)
        .edit()
        .putInt(recyclerScrollKey, position)
        .apply()
}</pre>
Note that <code>apply()</code> saves the preference in the background, if you're going to be rapidly returning to the <code>RecyclerView</code> you may need <code>commit()</code> as it saves immediately. Additionally, you may find <code>.findFirstVisibleItemPosition()</code> works better in your use case as it depends on average row height somewhat.
<h2>Loading the RecyclerView's scroll position</h2>
Loading the scroll position is even simpler, as <code>scrollToPosition()</code> is used:
<pre>override fun loadListPosition() {
    val scrollPosition = PreferenceManager.getDefaultSharedPreferences(activity!!).getInt(recyclerScrollKey, 0)
    myRecycler.scrollToPosition(scrollPosition)
}</pre>
Note that if you are currently drawing your <code>RecyclerView</code> under the toolbar / other views, you may need to (safely) manually adjust the <code>scrollPosition</code>. "Fully visible" actually only refers to "fully visible somewhere", even if it is under another view! In my case, the adjustment needed was:
<pre>override fun loadListPosition() {
    var scrollPosition = PreferenceManager.getDefaultSharedPreferences(activity!!).getInt(recyclerScrollKey, 0)
    if (scrollPosition &gt; 0 &amp;&amp; scrollPosition &lt; myRecycler.layoutManager.childCount) {
        scrollPosition++ // To offset the "completely visible" item under the action bar
    }
    myRecycler.scrollToPosition(scrollPosition)
}</pre>
<h2>Resetting the RecyclerView's scroll position</h2>
Finally, you'll likely want to regularly reset the scroll position if you change the <code>RecyclerView</code> content, the user logs out, etc. This is done by just resetting the <code>SharedPreference</code>:
<pre>override fun resetSavedListPosition(){
    PreferenceManager.getDefaultSharedPreferences(activity!!)
        .edit()
        .putInt(recyclerScrollKey, 0)
        .apply()
}</pre>
This post is also <a href="https://gist.github.com/JakeSteam/ce074069c98deb764b9d74e596b87a69" target="_blank" rel="noopener">available as a GitHub Gist</a>.