---
ID: 1680
post_title: >
  Dynamically preventing scrolling on
  selected ViewPager pages
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/dynamically-preventing-scrolling-on-selected-viewpager-pages/
published: true
post_date: 2018-09-06 21:12:05
---
ViewPagers are an extremely powerful UI tool that by default can be swiped left and right freely. In some cases however, it can be useful to prevent the user swiping in certain directions on certain pages, i.e. a "LockableViewPager". For example, the first 2 pages might have to be passed programmatically, and then all other pages can be navigated between freely.

This article will implement determining and changing at any time the current permitted swipe direction(s) (left, right, both, neither) using a custom ViewPager, concluding with a full use case. The end result of this article is also available as a <a href="https://gist.github.com/JakeSteam/d69275118bd47984e94ee40d00aee219" target="_blank" rel="noopener">Gist</a>.

<!--more-->
<h3>Including custom element</h3>
Just replacing <code>ViewPager</code> with the full path of your <code>LockableViewPager</code> is the only change needed in your layout XML.
<pre>
&lt;com.example.LockableViewPager
        android:id="@+id/pager"
        android:layout_width="match_parent"
        android:layout_height="match_parent" /&gt;</pre>
<h3>LockableViewPager</h3>
First, a new class extending <code>ViewPager</code> has to be created, as well as values for the <code>initialXValue</code> (used to determine swipe direction) and <code>direction</code> (used to store permitted swipe direction):
<pre>
class LockableViewPager(context: Context, attrs: AttributeSet) : ViewPager(context, attrs) {

    private var initialXValue: Float = 0f
    private var direction: SwipeDirection? = null</pre>
Additionally, an enum of the possible scroll directions needs to be defined outside the class:
<pre>
enum class SwipeDirection { BOTH, LEFT, RIGHT, NONE }</pre>
Next, wrappers around the existing <code>onTouchEvent</code> and <code>onInterceptTouchEvent</code> functions have to be added, so any attempts to move between pages can be checked before being acted on:
<pre>
    override fun onTouchEvent(event: MotionEvent): Boolean {
        return if (this.isSwipeAllowed(event)) {
            super.onTouchEvent(event)
        } else false
    }

    override fun onInterceptTouchEvent(event: MotionEvent): Boolean {
        return if (this.isSwipeAllowed(event)) {
            super.onInterceptTouchEvent(event)
        } else false
    }</pre>
Now, the <code>isSwipeAllowed</code> function from the previous wrappers has to be implemented, returning a boolean. If the permitted direction is <code>BOTH</code>, <code>true</code> can be returned instantly, and the same for <code>NONE</code> and returning false.

When the <code>MotionEvent.ACTION_DOWN</code> is fired, the <code>initialXValue</code> is updated, so we know where the swipe started. When any subsequent <code>MotionEvent.ACTION_MOVE</code> event occurs, the <code>initialXValue</code> and swipe event's <code>x</code> can be used to calculate which way the user is swiping. The function can then return whether or not the swipe event is in a permitted direction.
<pre>
    private fun isSwipeAllowed(event: MotionEvent): Boolean {
        if (this.direction === SwipeDirection.BOTH) {
            return true
        } else if (direction === SwipeDirection.NONE) {
            return false
        }

        if (event.action == MotionEvent.ACTION_DOWN) {
            initialXValue = event.x
            return true
        }

        if (event.action == MotionEvent.ACTION_MOVE) {
            try {
                val diffX = event.x - initialXValue
                if (diffX &gt; 0 &amp;&amp; direction === SwipeDirection.RIGHT) {
                    // swipe from left to right detected
                    return false
                } else if (diffX &lt; 0 &amp;&amp; direction === SwipeDirection.LEFT) {
                    // swipe from right to left detected
                    return false
                }
            } catch (e: Exception) {
                e.printStackTrace()
            }
        }
        return true
    }</pre>
Finally, a simple function for setting the permitted swipe direction is added, and then the core of the LockableViewPager is finished.
<h3>Using LockableViewPager</h3>
To use the new <code>LockableViewPager</code>, just set your desired swipe direction during <code>onCreate</code> / <code>onViewCreated</code>:
<pre>
pager.setAllowedSwipeDirection(SwipeDirection.LEFT)</pre>
<h3>Example use case: unskippable pages</h3>
This code was originally used for inviting other users to an account. In this use case, some users were required (e.g. couldn't be skipped), whereas others were optional (e.g. could be skipped). As such, the required users were displayed first, and had to be passed by completing an invite process which then programmatically moved to the next page. Required users could not be swiped away, but the subsequent optional users could be swiped between freely.

First, during the <code>onCreate</code> / <code>onViewCreated</code>, a custom page change listener is set using <code>pager.addOnPageChangeListener(pageChangeListener())</code> so that swipe logic can be updated whenever a new page is navigated to. This listener is defined as:
<pre>
    private fun pageChangeListener(): ViewPager.SimpleOnPageChangeListener =
        object : ViewPager.SimpleOnPageChangeListener() {
            override fun onPageSelected(position: Int) {
                setSwipeability()
            }
        }</pre>
<code>setSwipeability</code> is just a wrapper around <code>pager.setAllowedSwipeDirection(getSwipeDirection(pager))</code>, which calls the main logic <code>getSwipeDirection</code>.

First, if the current user is required, no swiping is permitted:
<pre>
        if (isDriverRequired(pager.currentItem)) {
            return SwipeDirection.NONE
        }</pre>
Next, various useful but simple values are calculated, to ensure there are no unreadably complicated boolean logic statements. The number of pages in the <code>LockableViewPager</code> is used extensively, and each statement builds on the last to avoid repeated logic.
<pre>
        val isFirstUser = pager.currentItem == 0
        val isLastUser = pager.currentItem == pager.adapter!!.count - 1
        val isUserToLeft = !isFirstUser &amp;&amp; pager.currentItem &gt; 0
        val isUserToRight = !isLastUser &amp;&amp; pager.currentItem &lt; pager.adapter!!.count - 1
        val isOptionalUserOnLeft = isUserToLeft &amp;&amp; !isUserRequired(pager.currentItem - 1)
        val isOptionalUserOnRight = isUserToRight &amp;&amp; !isUserRequired(pager.currentItem + 1)</pre>
Now that all the information required to calculate the permitted swipe directions has been calculated, the actual end logic is extremely simple:
<pre>
        if (isOptionalUserOnLeft &amp;&amp; isOptionalUserOnRight) {
            return SwipeDirection.BOTH
        } else if (isOptionalUserOnLeft) {
            return SwipeDirection.LEFT
        } else if (isOptionalUserOnRight) {
            return SwipeDirection.RIGHT
        } else {
            return SwipeDirection.NONE
        }</pre>
<h3>Conclusion</h3>
Whilst the initial idea of having varying swipe options on a per-page basis seems simple, the default <code>ViewPager</code> has no capabilities for this. Luckily, the extension described in this post adds the functionality in a very easy to use way, and has no noticeable performance impact.

Further improvements would be adding an optional "bounce" animation when trying to navigate in a non-permitted direction, instead of just ignoring the swipe. The <code>getSwipeDirection</code> function could also be improved by reducing the amount of if statements and distinct boolean statements, albeit at a risk of decreased readability.

As mentioned before, all of this <a href="https://gist.github.com/JakeSteam/d69275118bd47984e94ee40d00aee219" target="_blank" rel="noopener">code is available as a Gist</a>. Additionally, the core locking idea is originally from <a href="https://stackoverflow.com/a/34076649/608312" target="_blank" rel="noopener"><b>andre719mv</b>'s answer on StackOverflow</a>.