---
ID: 2315
post_title: 'Accessing a deep link intent&#8217;s data / URL in a fragment with AndroidX'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/accessing-a-deep-link-intents-data-url-in-a-fragment-with-androidx/
published: true
post_date: 2019-01-04 18:00:51
---
Usually, accessing the data associated with a deep link intent in an Activity is just a case of calling <code>intent.data</code>. Easy!

However when this intent is forwarded to a fragment (using the <a href="https://developer.android.com/topic/libraries/architecture/navigation/navigation-implementing" target="_blank" rel="noopener">AndroidX navigation</a> code below), the intent suddenly becomes much harder to deal with. In the fragment there is no <code>onNewIntent</code>, or even any intent at all. This tutorial will cover how to retrieve the intent's data, including deep link URL.
<pre>override fun onNewIntent(intent: Intent?) {
    super.onNewIntent(intent)
    my_nav_host_fragment.findNavController().onHandleDeepLink(intent)
}</pre>
<!--more-->

When the intent was forwarded to the fragment, it was significantly changed. It has now been packaged into <code>arguments</code>, which contains an ArrayMap called <code>mMap</code>, as well as a few other properties.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/1mahzi3.png"><img class="aligncenter size-full wp-image-2316" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/1mahzi3.png" alt="" width="540" height="324" /></a>

If you want to access this ArrayMap (for example to access the URL in the deep link), you can just call <code>arguments?.get(yourKey)</code>. However, what if you don't know the key, since there's no obvious way of looking it up?

In that case you need to call <code>arguments?.keySet()</code>, to receive an array of the <strong>keys</strong> the main ArrayMap uses. For example, my intent only had the deep link intent as a key:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/2.png"><img class="aligncenter size-full wp-image-2317" src="https://blog.jakelee.co.uk/wp-content/uploads/2019/01/2.png" alt="" width="426" height="238" /></a>

Now that you've got your key, you can call <code>arguments?.get(yourKey) as Intent</code>, to get your original intent back! This intent can then be queried as usual, such as <code>.data.encodedPath</code> to get the URL's path.

Here's a useful function that can be called in a fragment to determine if the current arguments are from a deep link for your target path:
<pre>fun isTargetPath(): Boolean {
    val deepLinkKey = arguments?.keySet()?.firstOrNull() ?: ""
    if (deepLinkKey.isNotEmpty() &amp;&amp; arguments?.get(deepLinkKey) is Intent) {
        val targetPath = "/mytargetpath"
        return (arguments?.get(deepLinkKey) as Intent).data.encodedPath == targetPath
    }
    return false
}</pre>
This should go in your onViewCreated, to avoid it being called repeatedly if the use pauses / resumes your fragment.