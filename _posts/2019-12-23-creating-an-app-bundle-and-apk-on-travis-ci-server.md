---
ID: 2624
post_title: >
  How to create an app bundle (and APK) on
  Travis / CI server
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-an-app-bundle-and-apk-on-travis-ci-server/
published: true
post_date: 2019-12-23 17:00:01
---
App bundles are one of my <a href="https://www.youtube.com/watch?v=Z-yJTjbswhw" target="_blank" rel="noopener noreferrer">5 Android techniques for 2020</a>, and with good reason: they're a low effort way of drastically reducing app size. Surprisingly, we also noticed faster times creating an app bundle then an APK than creating an APK directly. Of course, moving away from APKs is trickier if you have a complicated multi-stage build process involving QA or deployment!

Currently I am transitioning a large app slowly from APKs to app bundles whilst retaining compatibility with our existing processes. For now this means generating both app bundles and APKs. Whilst creating both isn't especially tricky, the detailed explanations in this post should make it easier to implement into Travis.

<!--more-->
<h2>The plan</h2>
Our approach is going to consist of 4 steps:
<ul>
 	<li>Preparing Travis to use a build script</li>
 	<li>Making an app bundle for our app</li>
 	<li>Downloading bundletool, so we can...</li>
 	<li>...generate a universal APK from our app bundle</li>
</ul>
If you'd like to see a finished script of this post (recommended), it's available as a Gist: <a href="https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f" target="_blank" rel="noopener noreferrer">https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f</a>
<h3>Preparing Travis / CI config</h3>
Before we write anything, we need to make our CI config (e.g. <code>.travis.yml</code>) able to run a shell script! Otherwise, our build config is going to get very messy. These steps are going to vary a little bit between CIs, but should be pretty similar.
<ol>
 	<li>Before anything else, make sure the build scripts can be executed by changing their permissions (assuming they're in <code>/build-scripts/</code>):
<pre>before_install: # Make sure build scripts can be executed
      - chmod 755 build-scripts/*.sh</pre>
</li>
 	<li>Next, add your script to the <code>script</code> step, passing the built-in build directory variable:
<pre>script: # Runs build script
  - build-scripts/travis_build.sh $TRAVIS_BUILD_DIR</pre>
</li>
</ol>
Our CI is now going to run <code>build-scripts/travis_build.sh</code>, so let's make that file and make it do something!
<h3>Making an app bundle</h3>
This is amazingly simple, we just need to run the gradle task for bundle generation inside our <code>travis_build.sh</code> file:
<pre>./gradlew app:bundleDebug</pre>
If you're using build variants (e.g. <code>prodDebug</code>), you'll need to do <code>app:bundleProdDebug</code> instead. This would also be a good place to do <code>lintDebug</code>, <code>ktlint</code>, or any other pre-build checks by adding them to the list of gradle tasks.
<h3>Downloading bundletool</h3>
To convert our app bundle into a universal APK (an APK that can be installed on any device), we'll need <a href="https://developer.android.com/studio/command-line/bundletool" target="_blank" rel="noopener noreferrer">bundletool</a>. This is just a case of <code>curl</code>ing it:
<pre>curl -O -L "https://github.com/google/bundletool/releases/download/0.11.0/bundletool-all-0.11.0.jar"</pre>
<h3>Using bundletool to create a universal APK</h3>
To create our universal APK we'll need our keystore information. This can be hardcoded, passed in, stored in environment variables, retrieved from a remote server etc, but we'll be keeping it simple. We're also storing a debug keystore in our repo for simplicity, and passing in the bundle &amp; output locations. Travis' build directory is passed in from our build config.
<pre># Get the build directory from build config
TRAVIS_BUILD_DIR=$1

# Use bundletool to create universal .apks zip
java -jar bundletool-all-0.11.0.jar build-apks \
    --mode=universal \
    --bundle=${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/app.aab \
    --output=${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/app.apks \
    --ks=${TRAVIS_BUILD_DIR}/app/build-extras/debug.keystore \
    --ks-pass=pass:android \
    --ks-key-alias=androiddebugkey \
    --key-pass=pass:android;</pre>
Bundletool actually creates an <code>.apks</code> file, a zip file of our universal APK. We can get our actual APK with a quick unzip into a new <code>unzipped</code> folder:
<pre>unzip ${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/app.apks -d ${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/unzipped;</pre>
And that's it! We now have our universal APK at <code>${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/unzipped/universal.apk</code>, ready to be sent to wherever wants an APK.
<h2></h2>
<h2>Tidying up</h2>
Whilst the above approach does achieve the goal, the code is pretty messy and hard coded. Extracting variables and making the code more generic will let us use it for debug and release builds, as well as making it easier to modify.
<h3>Extracting variables</h3>
Inside our <code>travis_build.sh</code> script, we should extract all of our file locations and keystore information. Once this is done, it will be possible to pass the variables to a generic function that can handle any build type. For example:
<pre># Debug build variables
DEBUG_BUNDLE_PATH=${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodDebug/
DEBUG_KEYSTORE_PATH=${TRAVIS_BUILD_DIR}/app/build-extras/debug.keystore
DEBUG_KEYSTORE_PASSWORD=android
DEBUG_KEYSTORE_ALIAS=androiddebugkey
DEBUG_KEYSTORE_KEY_PASSWORD=android

# Release build variables
RELEASE_BUNDLE_PATH=${TRAVIS_BUILD_DIR}/app/build/outputs/bundle/prodRelease/
RELEASE_KEYSTORE_PATH=${TRAVIS_BUILD_DIR}/app/build-extras/upload-keystore.jks
RELEASE_KEYSTORE_PASSWORD=mypassword
RELEASE_KEYSTORE_ALIAS=uploadkey
RELEASE_KEYSTORE_KEY_PASSWORD=mykeypassword</pre>
<h3>Extracting into functions</h3>
Converting our script into functions will simplify the overall logic, and help clarify the code's meaning. I chose to split my code into "bundle generating" and "bundle to apk conversion" functions. The converter also includes a quick check for the number of parameters passed:
<pre>function compileAndTestDebugBundle {
    echo "Compiling and testing a debug bundle!"
    ./gradlew app:bundleDebug
}

function convertBundleToApk {
    # Check all necessary arguments have been passed
    if [[ "$#" -eq 5 ]]; then
        echo "Converting bundle to APK!"
    else
        echo "Make sure you pass in a bundle path, and all 4 keystore values!"
        exit 1
    fi

    # Download bundletool
    curl -O -L "https://github.com/google/bundletool/releases/download/0.11.0/bundletool-all-0.11.0.jar"

    # Use bundletool to create universal .apks zip
    java -jar bundletool-all-0.11.0.jar build-apks \
        --mode=universal \
        --bundle=${1}app.aab \
        --output=${1}app.apks \
        --ks=${2} \
        --ks-pass=pass:${3} \
        --ks-key-alias=${4} \
        --key-pass=pass:${5};

    # Unzip .apks zip into /unzipped
    unzip ${1}app.apks -d ${1}unzipped;
}</pre>
Now, we can just call our functions (with the parameters) to perform the task, making the code's intention much more obvious. Making a release build is as simple as passing different parameters, easy!
<pre>compileAndTestDebugBundle;
convertBundleToApk ${DEBUG_BUNDLE_PATH} ${DEBUG_KEYSTORE_PATH} ${DEBUG_KEYSTORE_PASSWORD} ${DEBUG_KEYSTORE_ALIAS} ${DEBUG_KEYSTORE_KEY_PASSWORD}</pre>
<h3>Securing passwords</h3>
Whilst our approach so far works well, it isn't particularly secure. Our keystore passwords are in our build config, meaning they're visible to anyone who can see our CI logs or repository. Travis' encrypted environment variables are a great solution to this. They encrypt your password (e.g. <code>KEYSTORE_PASSWORD</code> and <code>KEYSTORE_KEY_PASSWORD</code>, and only provide it to you during a build (<a href="https://blog.jakelee.co.uk/retrieving-forgotten-environment-variables-from-travis-ci/" target="_blank" rel="noopener noreferrer">they can be retrieved though!</a>). This can then be passed to your script and used by changing the build config to:
<pre>script: # Runs build script
  - build-scripts/travis_build.sh $TRAVIS_BRANCH $KEYSTORE_PASSWORD $KEYSTORE_KEY_PASSWORD</pre>
And your variable definitions to:
<pre>RELEASE_KEYSTORE_PASSWORD=$2
RELEASE_KEYSTORE_KEY_PASSWORD=$3</pre>
<h3>Next steps</h3>
Whilst <a href="https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f" target="_blank" rel="noopener noreferrer">the results of this post</a> are enough to create app bundles and APKs, there's always improvements to be made. For example, the functions could be extracted to a new <code>.sh</code> file, then included in <code>travis_build.sh</code>.

You could also perform a release build for specific branches, by passing the <code>TRAVIS_BRANCH</code> environment variable from the Travis config. This value can then be used in a simple if statement to create a build:
<pre>if [[ ${TRAVIS_BRANCH} == "dev" ]]; then
    compileAndTestReleaseBundle;
    convertBundleToApk ${RELEASE_BUNDLE_PATH} ${RELEASE_KEYSTORE_PATH} ${RELEASE_KEYSTORE_PASSWORD} ${RELEASE_KEYSTORE_ALIAS} ${RELEASE_KEYSTORE_KEY_PASSWORD}
    uploadToDeployGate;
else
    compileAndTestDebugBundle;
    convertBundleToApk ${DEBUG_BUNDLE_PATH} ${DEBUG_KEYSTORE_PATH} ${DEBUG_KEYSTORE_PASSWORD} ${DEBUG_KEYSTORE_ALIAS} ${DEBUG_KEYSTORE_KEY_PASSWORD}
    uploadToDeployGate;
fi</pre>
Finally, the next step for me will be moving away from APKs completely, by using <a href="https://support.google.com/googleplay/android-developer/answer/9303479?hl=en-GB" target="_blank" rel="noopener noreferrer">Google Play Internal App Sharing</a> for QA. This won't be until the new year, and a post will be made!

PS: Just in case anyone missed it, here's the outcome of this post: <a href="https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f" target="_blank" rel="noopener noreferrer">https://gist.github.com/JakeSteam/eacc45ddd0db942d6902150f09dfa39f</a>

PPS: Happy holidays!