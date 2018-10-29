---
ID: 1790
post_title: >
  Adding Callbacks From Fragment To
  Activity Or Activity To Application In
  Android
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/adding-callbacks-from-fragment-to-activity-or-activity-to-application-in-android/
published: true
post_date: 2018-10-24 09:00:23
---
When navigating between components in your Android app, you'll sometimes want to call back to the component's parent. If your navigation uses activities, <code>startActivityForResult()</code> is easy to use and works fine. However, if your app is a more modern app and uses fragments, this won't work, and there is no <code>startFragmentForResult()</code> function! It can also be helpful for the application class to know the result from a specific activity.

One solution (among others) to this problem is to define an interface with a callback that the parent component can implement, and the child component can obtain a reference to and then call. If that sounds a little complicated, the code should clarify!

This post is also <a href="https://gist.github.com/JakeSteam/868e9262ba540d38a2fca7ca56808b88" target="_blank" rel="noopener">available as a Gist</a>.

<!--more-->
<h2>The interface</h2>
The interface (I've called mine <code>ActionHandler</code>) just needs to define a <code>handleAction</code> function, which takes a single string as a parameter.
<pre>
interface ActionHandler {
    fun handleAction(actionCode: String)
}</pre>
<h2>The parent</h2>
The parent activity or application needs to implement <code>ActionHandler</code> (e.g. <code>MainActivity : ActionHandler {</code>) and override <code>handleAction</code>. For example, the activity may need to perform different actions based on which fragment has just been closed.
<pre>
    override fun handleAction(actionCode: String) {
        when {
            actionCode == FragmentA.FRAGMENT_A_CLOSED -&amp;gt; {
                doSomething()
            }
            actionCode == FragmentB.FRAGMENT_B_CLOSED -&amp;gt; {
                doSomethingElse()
            }
            actionCode == FragmentC.FRAGMENT_C_CLOSED -&amp;gt; {
                doAnotherThing()
            }
        }
    }</pre>
<h2>The child</h2>
The child fragment or activity needs to have a public constant defined in the companion, used to check which callback is being triggered.
<pre>
    companion object {
        const val FRAGMENT_A_CLOSED = "com.example.fragment_a_closed"
    }</pre>
Now, when the fragment should be closed (e.g. if it's a modal and the user has pressed the close button), try to call the parent's callback. It's worth wrapping this in a try/catch in case the fragment has accidentally been opened from another activity. The parent should never be null, as an activity can't live without an application, and a fragment can't live without an activity!
<pre>
            try {
                (activity as ActionHandler).handleAction(FRAGMENT_A_CLOSED)
            } catch (e: ClassCastException) {
                Timber.e("Calling activity can't get callback!")
            }
            dismiss()</pre>
That's it! Now, when the child calls <code>handleAction</code>, the parent will receive the callback, and can perform any action needed.
<h2>Next steps</h2>
If additional data should be passed back, an additional parameter can be added to the interface for the child to send back to the parent. The child does not also necessary have to close for the parent to receive the callback, even though receiving a callback on fragment close is the most common use case.

It's also worth pointing out that <a href="https://stackoverflow.com/questions/6751583/is-there-a-method-that-works-like-start-fragment-for-result" target="_blank" rel="noopener">there are many alternative techniques</a>, but I found the method above to be the simplest and cleanest solution, as well as the easiest to add new functionality too.