---
ID: 2647
post_title: >
  Uploading an app bundle to Google Play
  Internal App Sharing from Travis CI
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/uploading-an-app-bundle-to-google-play-internal-app-sharing-from-travis-ci/
published: true
post_date: 2020-02-18 16:00:29
---
In a post towards the end of last year, I explained <a href="https://blog.jakelee.co.uk/creating-an-app-bundle-and-apk-on-travis-ci-server/">how to generate app bundles on your CI server</a> (in my case Travis). Now they're being generated, the next step is to send them somewhere!

Google Play Internal App Sharing is my chosen destination, since that allows QAs to easily download and test the app bundle.<!--more-->

This post assumes you already have a custom bash script running on your CI, and are already generating app bundles. If that isn't the case, please read the <a href="https://blog.jakelee.co.uk/creating-an-app-bundle-and-apk-on-travis-ci-server/" target="_blank" rel="noopener noreferrer">previous post in the series</a> (or view <a href="https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f" target="_blank" rel="noopener noreferrer">the end code</a>). You can view the end result of this post <a href="https://gist.github.com/JakeSteam/0c68243f7e90da9bea70362461d59f34" target="_blank" rel="noopener noreferrer">as a gist</a>.

Whilst the uploading process is simple, the biggest challenge is actually authenticating with Google! We'll cover that before diving into the code itself.
<h2>Creating a Google service account</h2>
To create a service account, you'll need access to Google Play Console and Google Developers Console. On Google Play Console, you'll need to make a service account. <a href="https://developers.google.com/android-publisher/getting_started#using_a_service_account" target="_blank" rel="noopener noreferrer">There are official instructions</a>, but there's a few steps missing.
<ol>
 	<li>Go to Settings -&gt; Developer account -&gt; API access, and click "Create service account" <a href="https://i.imgur.com/OjAdmA6.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>.</li>
 	<li>Click the "Google API console" link <a href="https://i.imgur.com/Ac5sM5Y.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>, then click "Create service account" again<a href="https://i.imgur.com/WLk1mob.png"> (screenshot)</a>.</li>
 	<li>Fill in the basic details, to help remember what the account is for <a href="https://i.imgur.com/8Yex41R.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>.</li>
 	<li>Give your new account the "Firebase App Distribution Admin" role <a href="https://i.imgur.com/lnlMcDJ.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>.</li>
 	<li>Create a key for the new account, and download it in JSON format <a href="https://i.imgur.com/Ly4lDt2.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>. This will download a JSON file <a href="https://i.imgur.com/rUr6296.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>.</li>
 	<li>Finally, your new service account will show up on Google Play Console, press "Grant access"<a href="https://i.imgur.com/ehMJ1oC.png" target="_blank" rel="noopener noreferrer"> (screenshot)</a>.</li>
</ol>
Next, we need to prepare our JSON file for our CI server.
<h2>Encrypting your config file with Travis CI</h2>
Since our JSON file contains sensitive information, we don't want it to be stored directly in our repo. Whilst this is essential for a public repository, it's a good habit for private repos too. Luckily, Travis has built-in <a href="https://docs.travis-ci.com/user/encrypting-files/" target="_blank" rel="noopener noreferrer">support for file encryption</a>!

These instructions are for a Mac, but a similar process will work on a Windows machine. Assume the service account config has been renamed to <code>serviceaccount.json</code> and placed in <code>/scripts/</code>:
<ol>
 	<li>Install Travis command line with <code>gem install travis</code>.</li>
 	<li>Log in to your GitHub account via Travis. You can either do this by entering your username, password, and 2FA code using <code>travis login</code>, or by <a href="https://stackoverflow.com/a/59992012/608312" target="_blank" rel="noopener noreferrer">using an application password</a>.</li>
 	<li>Make sure your Terminal is in your project root (so Travis knows which account to store the file against), then enter <code>travis encrypt-file /scripts/serviceaccount.json</code>.</li>
 	<li>This will make a new file <code>/scripts/serviceaccount.json.enc</code>, and output the decryption command to add to the <code>before_install</code> part of your <code>.travis.yml</code>. It will look something like:</li>
</ol>
<pre>openssl aes-256-cbc -K $encrypted_def446eb3abc_key -iv $encrypted_def446eb3abc_iv -in scripts/serviceaccount.json.enc -out scripts/serviceaccount.json -d</pre>
That's it! Make sure to <strong>delete your original file and commit your encrypted file</strong>.

Once that's done, sending the app bundle itself is pretty easy. To start off with and help explain the overall flow, here's the main function.
<h2>Preparing overall script structure</h2>
For this script, the overall control is handled by <code>uploadToInternalAppSharing</code>, which calls a few functions we'll be defining shortly. There's also a parameter check at the very start (as with all other functions in this post) and a few comments, just to help out any future maintainers.
<pre>function uploadToInternalAppSharing {
    if (($# != 2)); then echo "Please pass the bundle path and service account JSON location!"; exit 2; fi
    BUNDLE_LOCATION="${1}app.aab"
    AUTH_LOCATION="${2}"

    # Parse service account JSON for authentication information
    SERVICE_ACCOUNT_CONFIG=$(&lt;${AUTH_LOCATION})
    AUTH_SERVER=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.token_uri')
    AUTH_EMAIL=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.client_email')
    AUTH_KEY=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.private_key')
    echo "Retrieved service account from JSON!"

    # Generate JWT from authentication information
    JWT=$(getJwt "$AUTH_SERVER" "$AUTH_EMAIL" "$AUTH_KEY")
    echo "Generated JWT!"

    # Use JWT to authenticate with Google and retrieve an access token
    ACCESS_TOKEN=$(getAccessToken "$AUTH_SERVER" "$JWT")
    echo "Generated access token!"

    # Use access token to upload app bundle to Google Play Internal App Sharing
    URL=$(uploadBundle "$ACCESS_TOKEN" "$BUNDLE_LOCATION")
    echo "Uploaded app bundle to ${URL}"
}</pre>
Now that's in place, we need to actually implement the functionality! The rest of this post will walk through the above function, and the calls it makes.
<h2>Decrypt your encrypted file</h2>
Inside your <code>.sh</code> script (triggered in your <code>.travis.yml</code>), specify your decrypted auth file's location:
<pre>AUTH_LOCATION=<span class="pl-smi">${TRAVIS_BUILD_DIR}</span>/scripts/serviceaccount.json</pre>
Next, pass this (and your bundle's location) into <code>uploadToInternalAppSharing</code> so the file can be read:
<pre> SERVICE_ACCOUNT_CONFIG=<span class="pl-s"><span class="pl-pds">$(</span><span class="pl-k">&lt;</span><span class="pl-smi">${AUTH_LOCATION}</span><span class="pl-pds">)</span></span></pre>
Next, retrieve the necessary variables from this file. In this case, that's the token URI, email, and private key:
<pre>AUTH_SERVER=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.token_uri')
AUTH_EMAIL=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.client_email')
AUTH_KEY=$(echo ${SERVICE_ACCOUNT_CONFIG} | jq -r '.private_key')</pre>
We now pass these 3 variables into our <code>getJwt</code> function, storing the result: <code>JWT=<span class="pl-s"><span class="pl-pds">$(</span>getJwt <span class="pl-pds">"</span><span class="pl-smi">$AUTH_SERVER</span><span class="pl-pds">"</span> <span class="pl-pds">"</span><span class="pl-smi">$AUTH_EMAIL</span><span class="pl-pds">"</span> <span class="pl-pds">"</span><span class="pl-smi">$AUTH_KEY</span><span class="pl-pds">"</span><span class="pl-pds">)</span></span></code>.
<h2>Create JWT</h2>
Whilst the following block of code does look a bit intimidating, it's just performing the necessary manipulations to make a JWT (JSON Web Token). You can find more information about JWTs and validate their contents <a href="https://jwt.io/" target="_blank" rel="noopener noreferrer">at jwt.io</a>. We'll be using them to prove to Google that we are allowed to act on the behalf of the service account we created

The output of this function should be a fairly lengthy string of characters, which can be used for the next step. Note that the <code>EOF</code> <strong>must</strong> be without any indentation, no matter how tidy you usually keep your code!
<pre>function getJwt {
    if (($# != 3)); then echo "Please pass the auth server, auth email, and auth key!"; exit 2; fi
    AUTH_SERVER=$1
    AUTH_EMAIL=$2
    AUTH_KEY=$3

    # Create JWT header
    JWT_HEADER=$(echo -n '{"alg":"RS256","typ":"JWT"}' | openssl base64 -e)

    # Create JWT body
    JWT_BODY=$(cat &lt;&lt;EOF
    {
        "aud": "${AUTH_SERVER}",
        "iss": "${AUTH_EMAIL}",
        "scope": "https://www.googleapis.com/auth/androidpublisher",
        "exp": $(($(date +%s)+300)),
        "iat": $(date +%s)
    }
EOF
    )
    JWT_BODY_CLEAN=$(echo -n "$JWT_BODY" | openssl base64 -e)

    # Create complete payload
    JWT_PAYLOAD=$(echo -n "$JWT_HEADER.$JWT_BODY_CLEAN" | tr -d '\n' | tr -d '=' | tr '/+' '_-')

    # Create JWT signature
    JWT_SIGNATURE=$(echo -n "$JWT_PAYLOAD" | openssl dgst -binary -sha256 -sign &lt;(printf '%s\n' "$AUTH_KEY") | openssl base64 -e)
    JWT_SIGNATURE_CLEAN=$(echo -n "$JWT_SIGNATURE" | tr -d '\n' | tr -d '=' | tr '/+' '_-')

    # Combine JWT payload and signature
    echo ${JWT_PAYLOAD}.${JWT_SIGNATURE_CLEAN}
}</pre>
Once we have our JWT, it can be passed to our <code>getAccessToken</code> function, again saving the result: <code>ACCESS_TOKEN=<span class="pl-s"><span class="pl-pds">$(</span>getAccessToken <span class="pl-pds">"</span><span class="pl-smi">$AUTH_SERVER</span><span class="pl-pds">"</span> <span class="pl-pds">"</span><span class="pl-smi">$JWT</span><span class="pl-pds">"</span><span class="pl-pds">)</span></span></code>
<h2>Retrieve auth token</h2>
We now need to make a call to the auth server we retrieved from our service account config earlier. We're going to send it our JWT, and hopefully get back a token we can use to send content for the next few minutes. Notice the same <code>jq -r</code> technique as earlier used again.
<pre>function getAccessToken {
    if (($# != 2)); then echo "Please pass the auth server and JWT!"; exit 2; fi
    AUTH_SERVER=$1
    JWT=$2

    # Send JWT to auth server
    HTTP_RESPONSE=$(curl --silent --write-out "HTTPSTATUS:%{http_code}" \
      --header "Content-type: application/x-www-form-urlencoded" \
      --request POST \
      --data "grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer&amp;assertion=$JWT" \
      "$AUTH_SERVER")

    # Parse auth server response for body and status
    HTTP_BODY=$(echo ${HTTP_RESPONSE} | sed -e 's/HTTPSTATUS\:.*//g')
    HTTP_STATUS=$(echo ${HTTP_RESPONSE} | tr -d '\n' | sed -e 's/.*HTTPSTATUS://')

    # Check response status for success, and retrieve access token if possible
    if [[ ${HTTP_STATUS} != 200 ]]; then
        echo -e "Create access token failed.\nStatus: $HTTP_STATUS\nBody: $HTTP_BODY\nExiting."
        exit 1
    fi
    echo $(echo ${HTTP_BODY} | jq -r '.access_token')
}</pre>
Finally we're ready to actually send our bundle! We do this by sending our access token (and bundle location) to our new <code>uploadBundle</code> function. We keep track of the result as usual: <code>URL=<span class="pl-s"><span class="pl-pds">$(</span>uploadBundle <span class="pl-pds">"</span><span class="pl-smi">$ACCESS_TOKEN</span><span class="pl-pds">"</span> <span class="pl-pds">"</span><span class="pl-smi">$BUNDLE_LOCATION</span><span class="pl-pds">"</span><span class="pl-pds">)</span></span></code>
<h2>Upload to Internal App Sharing</h2>
This function is very similar to the access token fetching, except this time we're posting our bundle instead of our JWT. We're also going to use <code>--progress-bar</code> for a nice visual way of keeping track of upload process, very useful when a bundle gets into the 10s of MB. Don't forget to use your own package name!

This will finally return the URL we're after, which testers can use to download the app.
<pre>function uploadBundle {
    if (($# != 2)); then echo "Please pass the access token and bundle location!"; exit 2; fi
    ACCESS_TOKEN=$1
    BUNDLE_LOCATION=$2

    # Send app bundle and access token to internal app sharing
    PACKAGE="uk.co.jakelee.examplepackage"
    HTTP_RESPONSE=$(curl --write-out "HTTPSTATUS:%{http_code}" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "Content-Type: application/octet-stream" \
      --progress-bar \
      --request POST \
      --upload-file ${BUNDLE_LOCATION} \
      https://www.googleapis.com/upload/androidpublisher/v3/applications/internalappsharing/${PACKAGE}/artifacts/bundle?uploadType=media)

    # Parse internal app sharing response for body and status
    HTTP_BODY=$(echo ${HTTP_RESPONSE} | sed -e 's/HTTPSTATUS\:.*//g')
    HTTP_STATUS=$(echo ${HTTP_RESPONSE} | tr -d '\n' | sed -e 's/.*HTTPSTATUS://')

   # Check response status for success, and retrieve download URL if possible
   if [[ ${HTTP_STATUS} != 200 ]]; then
       echo -e "Upload app bundle failed.\nStatus: $HTTP_STATUS\nBody: $HTTP_BODY\nExiting."
       exit 1
   fi
   echo $(echo ${HTTP_BODY} | jq -r '.downloadUrl')
}</pre>
Now, when this script runs on your CI, you should see something like:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/02/UMOLAQe.png"><img class="size-full wp-image-2648 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/02/UMOLAQe.png" alt="" width="764" height="218" /></a>

If anything didn't quite work out, <a href="https://gist.github.com/JakeSteam/0c68243f7e90da9bea70362461d59f34" target="_blank" rel="noopener noreferrer">the gist of this post</a> may help! Otherwise, feel free to leave a comment and I'll help out.
<h2>Adding uploaders and testers</h2>
Before these internal apps can actually be used, we need to set up permissions for them. This is done from the application's page on Google Play Console, then Development Tools -&gt; Internal App Sharing.

On this page, select any other groups of people you'd like to be able to upload bundles manually, and any groups of QA / other testers <a href="https://i.imgur.com/tK10dtq.png" target="_blank" rel="noopener noreferrer">(screenshot)</a>.

Now, when one of your testers clicks the link generated above on an account with app sharing enabled, they'll be prompted to install the app. For information on enabling app sharing and more information on the system, <a href="https://support.google.com/googleplay/android-developer/answer/9303479?hl=en-GB" target="_blank" rel="noopener noreferrer">Google's documentation is pretty helpful</a>.
<h2>Next steps</h2>
Whilst it's excellent that testers can now download the app by looking at Travis logs, obviously this isn't an ideal solution. The next post in this series will cover notifying QA that a new build is available (likely through Slack, or email).

A possible improvement would be notifying developers if a part of the process fails, and perhaps retrying any failed attempts. However, in my experience the steps have been very reliable so this hasn't been necessary.

It's also very important to remember these builds will only be available for 60 days, and won't natively mention anything about which branch they're from, who made them, etc. As such, this has to be handled via alternative systems for now.
<h2>References</h2>
<ul>
 	<li>Encrypting files: <a href="https://docs.travis-ci.com/user/encrypting-files/">https://docs.travis-ci.com/user/encrypting-files/</a></li>
 	<li>Share app bundles and APKs internally:<a href="https://support.google.com/googleplay/android-developer/answer/9303479?hl=en-GB"> https://support.google.com/googleplay/android-developer/answer/9303479?hl=en-GB</a></li>
 	<li>Internal app sharing artifacts: <a href="https://developers.google.com/android-publisher/api-ref/internalappsharingartifacts">https://developers.google.com/android-publisher/api-ref/internalappsharingartifacts</a></li>
 	<li>Google PlayStore internal app sharing:<a href="https://www.brightec.co.uk/blog/google-playstore-internal-app-sharing"> https://www.brightec.co.uk/blog/google-playstore-internal-app-sharing</a></li>
 	<li>Google PlayStore and automated deployment: <a href="https://www.brightec.co.uk/blog/google-playstore-and-automated-deployment">https://www.brightec.co.uk/blog/google-playstore-and-automated-deployment</a></li>
 	<li>Google PlayStore automated deployment with AAB: <a href="https://www.brightec.co.uk/blog/google-playstore-automated-deployment-aab">https://www.brightec.co.uk/blog/google-playstore-automated-deployment-aab</a></li>
</ul>