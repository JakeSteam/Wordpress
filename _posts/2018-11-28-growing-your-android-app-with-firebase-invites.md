---
ID: 2074
post_title: >
  Growing Your Android App With Firebase
  Invites
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/growing-your-android-app-with-firebase-invites/
published: true
post_date: 2018-11-28 22:06:13
---
Firebase Invites provide a simple way to add user referral schemes to your app for free, without any additional resources. The system runs on top of Firebase Dynamic Links, and supports both email and SMS referrals.

Invite messages can be customised, and they can include custom data / deep links. This custom information works even if the user doesn't have the app installed when they initially click the referral link, making Invites an excellent way to build your app's user base.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, the <a href="https://firebase.google.com/docs/invites/android" target="_blank" rel="noopener">official documentation</a> and official intro video may be useful:

[embed]https://www.youtube.com/watch?v=LkaIJCZ_HyM[/embed]
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/10" target="_blank" rel="noopener">pull request for adding Firebase Invites</a> if you just want to see the code changes required. This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.

In this tutorial, you'll learn how to add the ability for users to send out invite links, and identify if a user has come from an invite link.
<h3>Adding Firebase Invites to your project</h3>
First, visit the Dynamic Links tab in the <a href="https://console.firebase.google.com/u/0/">Firebase console</a>, and agree to the terms of service (if necessary). Don't worry if there's no terms of service to agree, it only occurs in some situations!

Next, add Firebase Invites by adding the following to your dependencies in your app-level <code>build.gradle</code> file, then perform a Gradle sync:
<pre>implementation 'com.google.firebase:firebase-invites:16.0.5'</pre>
<h3>Sending invites</h3>
<h4>Displaying invite screen</h4>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/invites.png"><img class="alignright wp-image-2083 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/invites-228x300.png" alt="" width="228" height="300" /></a>The invite screen allows the user to customise the invite message, and select one or more email / SMS contacts. In the image below, you can see a user with SMS only (chat icon), email only (envelope icon), and both (envelope icon with arrow). Email addresses and phone numbers can also be manually entered.

To actually display this dialog, pass an <code>AppInviteInvitation</code> to <code>startActivityForResult()</code>, which can be customised using the builder. Only the dialog title and invitation message must be defined, the rest are optional.
<pre>private fun sendInviteOnClick() = activity!!
        .startActivityForResult(
                AppInviteInvitation.IntentBuilder("Share an invite!")
                        .setMessage("Here is the custom message inside the invitation!")
                        .setDeepLink(Uri.parse("https://firebasereference.jakelee.co.uk/deeplink"))
                        .setCustomImage(Uri.parse("https://via.placeholder.com/600x600/4286f4/000000?text=Invite+image!"))
                        .setCallToActionText("Accept invite")
                        .build(), INVITE_REQUEST_CODE)</pre>
<ul>
 	<li><code>setMessage</code> defines the text at the top of the invite dialog.</li>
 	<li><code>setDeepLink</code> defines the deep link that should be retrieve when an invited user opens the app.</li>
 	<li><code>setCustomImage</code> allows you to specify an image that should be sent along with email invites.</li>
 	<li><code>setCallToActionText</code> allows you to specify the text on the invite button in the email. Note that during my implementation, this didn't seem to have any effect.</li>
 	<li><code>setEmailHtmlContent</code> and <code>setEmailSubject</code> can also be used to completely replace the email invitation.</li>
</ul>
<code>INVITE_REQUEST_CODE</code> must be under ~32k, and identifies the result returned when the user closes the dialog.
<h4>Alerting user to sent invites</h4>
When the user closes the dialog, either due to successfully inviting other users or pressing back, <code>onActivityResult</code> is called. This is called on your activity, even if you started the dialog from a fragment!

Once the <code>requestCode</code> and <code>resultCode</code> have been checked, the <code>data</code> object can be passed to <code>AppInviteInvitations</code> to get a list of invitation IDs. These IDs are useful for keeping track of which invites have been accepted, since they are also available when opening an invite link.
<pre>override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)
    if (requestCode == InvitesFragment.INVITE_REQUEST_CODE) {
        if (resultCode == Activity.RESULT_OK) {
            val ids = AppInviteInvitation.getInvitationIds(resultCode, data!!)
            val idsString = ids.joinToString()
            Toast.makeText(this, "Invited: $idsString", Toast.LENGTH_LONG).show()
        } else {
            Toast.makeText(this, "Invite sending failed / cancelled: $resultCode", Toast.LENGTH_LONG).show()
        }
    }
}</pre>
<h3>Receiving invites</h3>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/zBBxqzi-1.png"><img class="alignright wp-image-2084 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/zBBxqzi-1-300x136.png" alt="" width="300" height="136" /></a><a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/invite.png"><img class="alignright wp-image-2090 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/invite-300x188.png" alt="" width="300" height="188" /></a>When a user clicks the dynamic invite link from an email or text, they are taken directly to your app (or to the Play Store to install your app). When this happens, you need to check with Firebase Dynamic Links if there are any unprocessed invitations. Note that this function returns success even if there are no new invites, the difference is in whether <code>data</code> is null or not.

This logic should be included on every activity / fragment in your app where you want to check the user's invite status, which is likely to be almost all.
<pre>private fun processPendingInvite() = FirebaseDynamicLinks.getInstance()
        .getDynamicLink(activity!!.intent)
        .addOnSuccessListener { data -&gt;
            if (data == null) {
                Log.d("Invites", "getInvitation: no data")
            } else {
                val deepLink = data.link
                val inviteId = FirebaseAppInvite.getInvitation(data).invitationId
                Toast.makeText(activity!!, "Retrieved deep link of $deepLink and an inviteId of $inviteId", Toast.LENGTH_LONG).show()
            }
        }
        .addOnFailureListener { Log.e("Invites", "Failed") }</pre>
Despite the documentation's claims, invitations do not reset themselves once used! Once <code>getDynamicLink</code> has returned an invitation, calling it again will return the same information. It is unknown if this is intentional, or a bug (version 16.0.5).
<h3>Additional notes</h3>
All SMS and Email invites use the user's Google Play details. Whilst this is fine for SMS, emails are often sent to spam folders. Additionally, the partially customised email invites don't seem to format very well. As such, using <code>setEmailHtmlContent</code> and <code>setEmailSubject</code> is recommended, to fully customise the invitation link.
<h2>Conclusion</h2>
Firebase Invites is a relatively simple to implement referral system, and whilst it does have a very limited feature set, it performs those features reliably. The <a href="https://firebase.google.com/docs/invites/best-practices">best practices article</a> provides a few useful tips,  and there are <a href="https://firebase.google.com/docs/invites/case-studies">two excellent simple implementations on the case studies page</a>.

The basic referral system may be useful for simple apps, especially those where a user is likely to download &amp; register for an app just to view a specific piece of content. However, a server should be used for management of these invite links, such as checking if an invite has been accepted. Surprisingly few games also use the feature, despite their dependence on referral &amp; invite mechanics.

It's also worth noting that for opening a related iOS app, <a href="https://firebase.google.com/docs/reference/android/com/google/android/gms/appinvite/AppInviteInvitation.IntentBuilder#setOtherPlatformsTargetApplication(int,%20java.lang.String)"><code>setOtherPlatformsTargetApplication</code> </a>must be used when displaying the invite dialog.

&nbsp;