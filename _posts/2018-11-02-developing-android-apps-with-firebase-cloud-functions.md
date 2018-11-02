---
ID: 1926
post_title: >
  Developing Android Apps With Firebase
  Cloud Functions
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/
published: true
post_date: 2018-11-02 17:12:58
---
Firebase Cloud Functions provide an easy way to execute JavaScript on Google's servers, and call this code from your own apps. It removes the need to manually manage any sort of server, and can be up and running very quickly. Firebase's free plan is somewhat limited, and cannot make network requests to other servers, but it can do plenty of processing.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/u/0/project/_/functions/list" target="_blank" rel="noopener">Firebase Cloud Functions dashboard</a>, and the <a href="https://cloud.google.com/functions/docs/" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/5" target="_blank" rel="noopener">pull request for adding Firebase Cloud Functions</a> if you just want to see the code changes required. Additionally, the hosted functions code is <a href="https://github.com/JakeSteam/FirebaseCloudFunctions" target="_blank" rel="noopener">available as a repository</a>.

This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.
<h3>Setting up Firebase Cloud Functions environment</h3>
<ol>
 	<li>First <a href="https://www.npmjs.com/get-npm" target="_blank" rel="noopener">install npm &amp; node.js</a>, used to handle the Firebase installation process.</li>
 	<li>Next, open a Command Prompt and enter <span style="font-family: 'courier new', courier, monospace;">npm install -g firebase-tools</span>, after a few minutes you should see something similar to the following image:<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/2.png"><img class="wp-image-1928 size-full aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/2.png" alt="" width="1179" height="619" /></a></li>
 	<li>Next, login to your Firebase account using <span style="font-family: 'courier new', courier, monospace;">firebase login</span>. This will open a browser with a login request. Once logged in, the Command Prompt will report success.<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/yZ61qY0.png"><img class="aligncenter wp-image-1931 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/yZ61qY0.png" alt="" width="860" height="229" /></a></li>
 	<li>Next run <span style="font-family: 'courier new', courier, monospace;">firebase init functions</span>, which will ask you a few questions about your setup. Click any of the following items to view a screenshot of the installation at that point.
<ol>
 	<li><a href="https://i.imgur.com/WD5ZNUk.png" target="_blank" rel="noopener">The project to use</a>, I chose my Firebase Reference Project.</li>
 	<li><a href="https://i.imgur.com/taBpPWI.png" target="_blank" rel="noopener">The language to use</a>, I chose JavaScript.</li>
 	<li><a href="https://i.imgur.com/h6b7TFp.png" target="_blank" rel="noopener">Whether style checker ESLint should be used &amp; dependencies should be installed</a>, I chose "Yes" to both.</li>
</ol>
</li>
 	<li>You should now have a project folder containing a few files and folders. <span style="font-family: 'courier new', courier, monospace;">functions/index.js</span> is the most important file, as it contains your functions' actual code. Open this file up and uncomment the lines relating to <span style="font-family: 'courier new', courier, monospace;">export.helloWorld</span>.</li>
</ol>
That's it, your local environment is now fully setup, and you've got a "Hello World" function ready to deploy!
<h3>Deploying Firebase Cloud Functions</h3>
To deploy or update your functions, enter <span style="font-family: 'courier new', courier, monospace;">firebase deploy --only functions</span> to your Command Prompt whilst inside your project directory. On Windows machines, this will fail by default due to a slightly faulty configuration (<a href="https://stackoverflow.com/questions/48345315/error-deploying-with-firebase-on-npm-prefix-resource-dir-run-lint" target="_blank" rel="noopener">this error specifically</a>):

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/7.png"><img class="size-full wp-image-1933 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/7.png" alt="" width="838" height="269" /></a>

Luckily, fixing this is just a case of opening your firebase.json and replacing <span style="font-family: 'courier new', courier, monospace;">\"$RESOURCE_DIR\"</span> with <span style="font-family: 'courier new', courier, monospace;">./functions/</span>. Save the changes, enter <span style="font-family: 'courier new', courier, monospace;">firebase deploy --only functions</span> again, and it should now successfully deploy:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/8.png"><img class="size-full wp-image-1934 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/8.png" alt="" width="689" height="335" /></a>

Our Hello World function is now ready to call! Visiting the URL outputted (<a href="https://us-central1-fir-referenceproject.cloudfunctions.net/helloWorld" target="_blank" rel="noopener">https://us-central1-fir-referenceproject.cloudfunctions.net/helloWorld</a>) will display whatever message our Cloud Function in <span style="font-family: 'courier new', courier, monospace;">index.js</span> was set to output. You now have a cloud-hosted function, congratulations! Time to make your own...
<h3>Creating a Cloud Function</h3>
You may have noticed the existing Hello World functions inside <span style="font-family: 'courier new', courier, monospace;">index.js</span> uses <span style="font-family: 'courier new', courier, monospace;">functions.https.onRequest</span>. This means it can be visited / called like a normal webpage, and has <span style="font-family: 'courier new', courier, monospace;">Content-Type</span> headers etc. Whilst you can get URL parameters from these methods (using <span style="font-family: 'courier new', courier, monospace;">request.query.text</span>), it's safer to provide actual functions that can be called remotely. There's a reason it's called Cloud <strong>Functions</strong>!

We're going to create a function that receives a phone's manufacturer's name from the app, converts it to uppercase, and then returns it to be shown in the app. This is obviously a pretty useless function that would usually be handled entirely inside the app, but it's a simple way to show passing data back and forth.

To do this, use <span style="font-family: 'courier new', courier, monospace;">functions.https.onCall</span> instead, which can also receive a data object with any additional parameters you've chosen to send. We're going to receive a manufacturer parameter,  so first check it has a length, then convert whatever it contains to uppercase and return it. Add the following into your <span style="font-family: 'courier new', courier, monospace;">index.js</span>:
<pre>exports.uppercaseDeviceName = functions.https.onCall((data) =&gt; {
    if (data.manufacturer.length &gt; 0) {
        return data.manufacturer.toUpperCase();
    }
    return "Unknown";
});</pre>
Save and redeploy the functions, and your custom function is now ready to be called. Note that to return data we just used <span style="font-family: 'courier new', courier, monospace;">return x</span>, instead of the <span style="font-family: 'courier new', courier, monospace;">response.send(x)</span> required by <span style="font-family: 'courier new', courier, monospace;">onRequest</span> functions. Google's example <span style="font-family: 'courier new', courier, monospace;">index.js</span> contains <a href="https://github.com/firebase/quickstart-js/blob/master/functions/functions/index.js" target="_blank" rel="noopener">more complex examples</a> that may be useful for further work.

Time to programmatically call our new function from inside an app!
<h3>Calling Cloud Functions from an Android app</h3>
First, add the Firebase Cloud Functions dependency in your app-level <span style="font-family: 'courier new', courier, monospace;">build.gradle</span> file and perform a sync:
<pre><span style="font-family: 'courier new', courier, monospace;">implementation </span><span class="pl-s"><span style="font-family: 'courier new', courier, monospace;"><span class="pl-pds">'</span>com.google.firebase:firebase-functions:16.1.2</span><span class="pl-pds"><span style="font-family: 'courier new', courier, monospace;">'</span></span></span></pre>
Next, use the following to make a call to your new uppercaseDeviceName function, passing it a HashMap containing your device's manufacturer (as the manufacturer key), and displaying a Toast of the result:
<pre>FirebaseFunctions.getInstance()
		.getHttpsCallable("uppercaseDeviceName")
		.call(hashMapOf("manufacturer" to Build.MANUFACTURER))
		.continueWith { task -&gt;
			if (task.isSuccessful) {
				Toast.makeText(activity, "Uppercase manufacturer is: ${task.result!!.data}", Toast.LENGTH_SHORT).show()
			} else {
				Toast.makeText(activity, task.exception.toString(), Toast.LENGTH_SHORT).show()
			}
		}</pre>
In the example app, this is triggered inside an <span style="font-family: 'courier new', courier, monospace;">onClick()</span>.

Due to Firebase already knowing which project you're calling (due to the <span style="font-family: 'courier new', courier, monospace;">google-services.json</span>), just passing the function's name is enough to call the function. Pretty cool, right? If you encounter problems in getting a response from the server, the "Web interface" part of this post covers how to view server logs containing any runtime errors. ESLint should also alert you on deployment if your code contains incorrect syntax, helpful if JavaScript isn't one of your core languages.
<h3>Automatically triggering Cloud Functions</h3>
Whilst both the Hello World and Uppercase Manufacturer Name functions in this tutorial were triggered on demand, it's also possible to automatically call cloud functions when an event happens. Using Google's example, you could trigger functions when a new image is uploaded to Firebase Storage to store the location in Firebase Database, and create a thumbnail of the image using an external API.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/intensive.png"><img class="aligncenter wp-image-1935 size-large" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/intensive-1024x576.png" alt="" width="730" height="411" /></a>

To set up these triggers, define your function as a listener instead of an <span style="font-family: 'courier new', courier, monospace;">onCall</span> or <span style="font-family: 'courier new', courier, monospace;">onRequest</span>. For example, <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-firestore/">Cloud Firestore</a> has <span style="font-family: 'courier new', courier, monospace;">onCreate</span>, <span style="font-family: 'courier new', courier, monospace;">onUpdate</span>, <span style="font-family: 'courier new', courier, monospace;">onDelete</span>, and <span style="font-family: 'courier new', courier, monospace;">onWrite</span> listeners. Set these up in your index.js as if they were a normal function:
<pre>exports.yourFunctionName = functions.firestore
    .document('sampledata').onWrite((change, context) =&gt; {
      // All changes will be inside `change`
    });</pre>
Many Firebase services provide these listeners that your Cloud Functions can subscribe to. To see which listeners are available, please see the live documentation as all of these services are currently in Beta and subject to change:
<ul>
 	<li><a href="https://firebase.google.com/docs/functions/analytics-events">Analytics</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/auth-events">Authentication</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/firestore-events">Firestore</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/pubsub-events">Cloud Pub/Sub</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/gcp-storage-events">Cloud Storage</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/crashlytics-events">Crashlytics</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/database-events">Realtime Database</a></li>
 	<li><a href="https://firebase.google.com/docs/functions/rc-events">Remote Config</a></li>
</ul>
<h2>Web interface</h2>
The majority of Cloud Functions' interface is in Firebase, but certain actions (e.g. deleting a function) can only be performed in Google Cloud Platform, so both will be covered.
<h3>Firebase</h3>
<h4>Dashboard</h4>
The dashboard shows an overview of all of your current cloud functions, with basic information like region, node version, and memory allocation.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard.png"><img class="aligncenter size-large wp-image-1937" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard-1024x125.png" alt="" width="730" height="89" /></a>
<h4>Health</h4>
The Health tab shows an overview of your functions' error rates and overall performance (times invoked, latency, memory usage, network usage). Checking this regularly can help you stay on track of any errors, and provide a quick health check of your functions.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/performance.png"><img class="aligncenter size-large wp-image-1938" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/performance-1024x605.png" alt="" width="730" height="431" /></a>
<h4>Logs</h4>
The Logs tab provides a simple log viewer, that can be used for basic analysis of any runtime issues encountered. Logs can be searched, and filtered by function, log level, and time period. These are very useful when experiencing issues with your functions, as a full stack trace will appear here.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/logs.png"><img class="aligncenter size-large wp-image-1939" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/logs-1024x515.png" alt="" width="730" height="367" /></a>
<h4>Usage</h4>
This tab displays a simple chart of total invocations in a selected date range, and a link to your current quota. As it is essentially a repeat of the Health tab, it's not included here!
<h3>Google Cloud Platform</h3>
As Cloud Functions technically run on Google Cloud, some statistics can only be viewed there. After visiting the links, make sure to select the correct project using the dropdown at the top of the page.
<h4>Dashboard</h4>
<a href="https://console.cloud.google.com/apis/dashboard" target="_blank" rel="noopener">The dashboard page</a> shows a more comprehensive overview of all your Google Cloud services, and their recent performance.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard-1.png"><img class="aligncenter size-large wp-image-1940" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/dashboard-1-1024x244.png" alt="" width="730" height="174" /></a>
<h4>Quotas</h4>
<a href="https://console.cloud.google.com/apis/api/cloudfunctions.googleapis.com/quotas">The quotas page</a> shows your functions' usage against all applicable resource limits. For example, your plan is subject to read limits, write limits, CPU limits, traffic limits, DNS limits, build time limits, etc. You should occasionally check the graphs on this page to make sure no functions are misbehaving.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/quotas.png"><img class="aligncenter size-large wp-image-1941" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/quotas-1024x378.png" alt="" width="730" height="269" /></a>
<h4>Functions</h4>
The <a href="https://console.cloud.google.com/functions/list" target="_blank" rel="noopener">Cloud Platform functions overview tab</a> looks and functions almost exactly the same as the Firebase functions overview tab, but it has one key feature: The ability to delete functions!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/delete.png"><img class="aligncenter size-full wp-image-1942" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/delete.png" alt="" width="965" height="190" /></a>
<h2>Conclusion</h2>
Firebase's Cloud Functions service is extremely powerful, and should definitely be investigated if looking into server side processing or considering managing a server yourself.

However, for many use cases it can be simpler to perform basic functionality client-side. Afterwards, this data can then be passed straight to Firestore or Database instead of handling it with a function. Additionally, the ability to only write code in JavaScript (or TypeScript, a slightly extended variant) severely limits the usefulness. Many Android developers will have little to no JavaScript experience, so learning a whole new language with a whole host of <a href="https://developer.telerik.com/featured/seven-javascript-quirks-i-wish-id-known-about/" target="_blank" rel="noopener">unique quirks</a> can be a daunting experience.

Before rashly converting all of your node.js servers to cloud functions, consider running a test run. From this, you can see if the response times and quota fit your requirements. You may find that the ability to distribute the load over multiple regions causes unacceptable performance in some regions.

Previous: <a href="https://blog.jakelee.co.uk//2018/10/25/developing-android-apps-with-firebase-realtime-database/">Developing Android Apps With Firebase Realtime Database</a>