---
ID: 1743
post_title: >
  Developing Android Apps With Firebase
  Authentication
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-authentication/
published: true
post_date: 2018-09-29 01:01:57
---
Firebase Authentication provides an app with the ability to handle user registration, user logging in, and retrieving user data. It has the ability to integrate with phone-based authentication as well as common services such as Facebook, Twitter, and Github. This tutorial will cover the simplest integrations, email and Google account.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/project/_/authentication/users" target="_blank" rel="noopener">Firebase Authentication dashboard</a>, and the <a href="https://firebase.google.com/docs/auth/" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/2" target="_blank" rel="noopener">pull request for adding Firebase Authentication</a> if you just want to see the code changes required.

This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.
<h3>Adding dependencies</h3>
To add Firebase Authentication, only the Auth library is needed in your app-level <code>build.gradle</code> file:
<pre>
implementation 'com.firebaseui:firebase-ui-auth:4.1.0'</pre>
<h3>Enabling sign-in methods</h3>
Next, the required sign in methods need to be activated in <a href="https://console.firebase.google.com/project/_/authentication/providers" target="_blank" rel="noopener">the console</a>. For each, click the relevant row then toggle "Enable", whilst leaving the other settings as default.
<h4>Email</h4>
The first, email, is very simple. No extra work is required, and it also allows the user to set a password to log back into their account later.
<h4>Google</h4>
The second, Google account, requires some more work. The SHA-1 fingerprint of your keystore (used to verify a version of your app is legitimate) needs to be added to your <a href="https://console.firebase.google.com/project/_/settings/general/" target="_blank" rel="noopener">Settings page</a>. With your settings page open, a SHA-1 fingerprint can be added by pressing "Add Fingerprint". This should be done for both debug and release keystores, so that Firebase Auth works in development and when released.

A <a href="https://developers.google.com/android/guides/client-auth" target="_blank" rel="noopener">full guide to generating a SHA-1 fingerprint</a> is provided by Google.

Eventually, your settings page should look like the image below. If the SHA-1 fingerprints have not been added correctly, you will see error code 16 when attempting to use Google-based login.
<img class="alignnone size-full wp-image-1745" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/s1gy7yk.png" alt="s1gy7yk" width="964" height="675" />

Your Firebase project is now ready to receive authentication requests from your app, time to add them!
<h3>Signing in</h3>
Signing in just requires setting the login methods desired (called providers), and starting Firebase Auth's login activity along with a request code. Once the sign-in attempt is completed (successfully or unsuccessfully), <code>onActivityResult</code> will be called. The following function can be called when a login button is pressed.
<pre>
    private fun clickLogin() = startActivityForResult(
            AuthUI.getInstance()
                    .createSignInIntentBuilder()
                    .setAvailableProviders(listOf(
                            AuthUI.IdpConfig.EmailBuilder().build(),
                            AuthUI.IdpConfig.GoogleBuilder().build()))
                    .build(), REQUEST_CODE)</pre>
<h4>Customisation</h4>
The login experience can be customised to better fit your app. By default your app's default theme is used, but a custom theme can be set using <code>setTheme()</code> on the <code>SignInIntentBuilder</code>. Additionally, calling <code>setLogo()</code> allows a drawable (e.g. your app's logo) to be shown in the middle of the screen. Finally, custom Terms of Service and Privacy Policy links can be added using <code>setTosAndPrivacyPolicyUrls()</code>. The following snippet functions identically to that above, but is much more customised:
<pre>
    private fun clickCustomLogin() = startActivityForResult(
            AuthUI.getInstance()
                    .createSignInIntentBuilder()
                    .setAvailableProviders(listOf(
                            AuthUI.IdpConfig.EmailBuilder().build(),
                            AuthUI.IdpConfig.GoogleBuilder().build()))
                    .setLogo(R.drawable.ic_firebase)
                    .setTheme(R.style.AuthenticationCustomTheme)
                    .setTosAndPrivacyPolicyUrls(
                            "https://google.com",
                            "https://firebase.google.com")
                    .build(), REQUEST_CODE)</pre>
<h3>Validating login</h3>
Once <code>onActivityResult</code> is called, the result needs to be checked to ensure it:
<ol>
 	<li>Is the result of the Firebase Auth sign-in</li>
 	<li>Was successful</li>
</ol>
This can be done by ignoring other requests (by returning early), and checking the result code:
<pre>
   override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        if (requestCode != REQUEST_CODE) return
        if (resultCode == RESULT_OK) {</pre>
Alternatively, if the <code>resultCode</code> is not <code>RESULT_OK</code>, we can try and retrieve a useful error message from the data returned by Firebase, then display it to the user:
<pre>
       } else {
            IdpResponse.fromResultIntent(data)?.let {
                val errorCode = it.error?.errorCode
                val errorMessage = it.error?.localizedMessage
                Toast.makeText(activity, String.format(getString(R.string.authentication_login_error), errorCode, errorMessage), Toast.LENGTH_SHORT).show()
            }</pre>
<h3>Get user information</h3>
Now that login has been successful, we can lookup the current user in Firebase using <code>val user = FirebaseAuth.getInstance().currentUser</code> and start extracting data. The unix timestamp for app account created (<code>creationTimestamp</code>) and last sign in (<code>lastSignInTimestamp</code>) are stored in <code>user.metadata</code>.

Most of the useful information is stored within the <code>user.providerData</code>; an array of every service the user has authenticated to your app with. This will usually only have 1 service in it, and the data available does vary by provider. That being said, the following should usually be available on each provider:
<ul>
 	<li><code>providerId</code>: The provider's name, e.g. "firebase".</li>
 	<li><code>uid</code>: The user's ID for that provider, format varies between provider.</li>
 	<li><code>displayName</code>: The name the user has chosen on that provider.</li>
 	<li><code>email</code>: The email the user signed up to the provider with.</li>
 	<li><code>photoUrl</code>: A publicly accessible image URL containing the user's profile picture for that provider.</li>
</ul>
This data is shown in the <a href="https://github.com/JakeSteam/FirebaseReference/" target="_blank" rel="noopener">reference app</a>:
<img class="alignnone size-full wp-image-1746" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/v2fiwrl.png" alt="v2fiwrl" width="463" height="608" />
<h3>Signing out</h3>
Signing the user out is even easier than logging in, as the callback is included, instead of requiring a separate function:
<pre>
   private fun clickLogout() =
            AuthUI.getInstance()
                    .signOut(activity!!)
                    .addOnCompleteListener {
                        Toast.makeText(activity!!, getString(R.string.authentication_logged_out), Toast.LENGTH_SHORT).show()
                    }</pre>
<h2>Web interface</h2>
The web interface consists of 4 tabs for Authentication, as follows:
<h3>Users</h3>
This tab provides a list of all users signed up for your app, and allows searching for specific users and resetting their password or deleting / disabling their account.
<img class="alignnone size-full wp-image-1747" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/6dz8uhy.png" alt="6dz8uhy" width="969" height="220" />
<h3>Sign-in method</h3>
This tab consists of general options relating to signing in:
<ol>
 	<li>Authorised domains: A list of domains permitted to use OAuth redirects. This should not need to be modified.</li>
 	<li>One account per email address: An option controlling whether the same email address at multiple providers should be merged into one account or not. On by default.</li>
 	<li>Manage sign-up quota: By default, the same IP can register 100 accounts per hour. This can be increased or decreased if necessary.</li>
 	<li>Sign-in providers: A list of all potential sign-in providers (shown below), as well as guidance on how to configure them.</li>
</ol>
<img class="alignnone size-full wp-image-1748" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/09/cgfagey.png" alt="cgfagey" width="970" height="479" />
<h3>Templates</h3>
This tab enables customisation of various customer communications, such as the email verification email, SMS verification text, and the ability to use a custom mail server for all emails.
<h3>Usage</h3>
This tab outlines the amount of <a href="https://firebase.google.com/docs/auth/android/phone-auth" target="_blank" rel="noopener">phone verifications</a> used in the current month. US &amp; Canada &amp; India are allowed 10k combined total, and the rest of the world is also allowed 10k combined total.
<h2>Conclusion</h2>
Overall, Firebase Authentication is extremely easy to use, whilst still providing very advanced functionality. It is completely free, and the only limitation is the number of phone authentications permitted per month.

For many apps, the system will easily be enough to manage all user account needs, with or without an additional server behind it. For more advanced solutions, a <a href="https://firebase.google.com/docs/auth/android/custom-auth" target="_blank" rel="noopener">custom authentication</a> can be configured, or even <a href="https://firebase.google.com/docs/auth/android/anonymous-auth" target="_blank" rel="noopener">anonymous logins</a>. These usually extremely complicated functionalities are reduced to a few lines of code with Firebase, making it a serious contender for any new major app development.

Previous: <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Adding Firebase to an Android Project</a>
Next: <a href="https://blog.jakelee.co.uk//developing-android-apps-with-firebase-cloud-firestore/">Developing Android Apps With Firebase Cloud Firestore</a>