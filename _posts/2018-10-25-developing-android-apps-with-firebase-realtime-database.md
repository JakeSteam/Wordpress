---
ID: 1806
post_title: >
  Developing Android Apps With Firebase
  Realtime Database
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-realtime-database/
published: true
post_date: 2018-10-25 21:20:24
---
Firebase Realtime Database is the more traditional predecessor to <a href="https://blog.jakelee.co.uk//2018/10/22/developing-android-apps-with-firebase-cloud-firestore/">Firestore</a>, and is essentially a way to store data as JSON and sync it between all clients / servers. Google have also <a href="https://cloud.google.com/datastore/docs/firestore-or-datastore" target="_blank" rel="noopener">provided a full comparison</a>.

Note that the JSON structured format can be quite limiting, and often requires thinking outside the box to make your existing data format fit inside it.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/project/_/database" target="_blank" rel="noopener">Firebase Realtime Database dashboard</a>, and the <a href="https://firebase.google.com/docs/database/android/start/" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/4" target="_blank" rel="noopener">pull request for adding Firebase Realtime Database</a> if you just want to see the code changes required.

This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.
<h3>Setting up</h3>
As Google recommends Cloud Firestore, it can be a little tricky to actually create the traditional Realtime Database. On <a href="https://console.firebase.google.com/u/0/project/_/database" target="_blank" rel="noopener">the Fireside Database page</a>, scroll down to the Realtime Database banner and select "Create database".
<img class="alignnone size-full wp-image-1808" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/f6fuu6w.png" alt="f6fuu6w" width="932" height="274" />

Create the database in <b>test mode</b>, making sure to set up proper access control later on before the app gets released to a wider audience.

Next, add the dependency to your app-level <code>build.gradle</code> file:
<pre>implementation 'com.google.firebase:firebase-database:16.0.3'</pre>
That's it, it's implemented!
<h3>Getting data</h3>
Setting up an automatic data change listener is the most convenient way to be notified on any data changes, including when the activity / fragment starts up. To do this, select the specific node to be monitored, and add a listener. In the following example, the node "nestedData" is used:
<pre>     FirebaseDatabase.getInstance().getReference("nestedData").addValueEventListener(object : ValueEventListener {
            override fun onDataChange(dataSnapshot: DataSnapshot) {
                displayData(dataSnapshot.value.toString())
            }

            override fun onCancelled(error: DatabaseError) {
                showToast("Failed to read value: ${error.toException()}")
            }
        })</pre>
As all data is stored as a giant JSON object, advanced filtering is not possible. Child nodes can be directly selected, for example <code>.getReference("nestedData").child("childNode")</code>, and there is some limited filtering available (<a href="https://firebase.google.com/docs/database/android/lists-of-data" target="_blank" rel="noopener">official documentation</a>).
<h4>Filtering</h4>
A few filtering methods are available, but are limited to selecting the first or last x results, or displaying results where a value is above, below, or equal to a specified value. These can also be combined, the table below explains these further:
<img class="alignnone size-full wp-image-1809" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/jrj4qpj.png" alt="jrj4qpj" width="855" height="226" />
<h4>Ordering</h4>
Additionally, child nodes <b>can</b> be ordered somewhat, by using <code>.orderByKey</code>, <code>.orderByValue</code>, or <code>.orderByChild("xyz")</code>. This can be combined with the previously described filtering methods, but only one ordering technique can be used at once.
<h3>Inserting data</h3>
One advantage of Realtime Database is it can serialise your objects into JSON for you, keeping all parameter names intact. The examples below use the data class <code>DataRow</code>, a simple class containing just <code>uuid</code> and <code>number</code>:
<pre>data class DataRow(val uuid: String, val number: String)</pre>
Additionally, <code>nestedData</code> is the node all operations are performed on, a reference to it is obtained via <code>FirebaseDatabase.getInstance().getReference("nestedData")</code>.
<h4>Automatic ID</h4>
To get Firebase to automatically create an ID, <code>.push()</code> to a node, then retrieve the key immediately afterwards. This new child node can then be written to.
<pre>        val key = nestedData.push().key
        key?.let {
            nestedData.child(key).setValue(DataRow("thisisauuid", 1234))
        }</pre>
This child node will now be saved under a random ID inside <code>nestedData</code>, with a key similar to "-LPc3jx0y-Hg6aITpvw2".
<h4>Manual ID</h4>
To manually set the ID, just write to your desired ID and the node will be created for you. This can be used for multiple levels of nesting to instantly create a multi-node data structure.

[code]
nestedData.child(&quot;collection&quot;).child(&quot;group&quot;).setValue(DataRow(&quot;anotheruuid, 4321&quot;))
[/code]

<h3>Updating data</h3>
Updating data is almost identical to adding new rows, you just need to set the new value of a node. The example below loops through every returned node, retrieves the key, then uses that to get a reference to the updateable node. It then sets the "uuid" value to the current value, except with an asterisk before it.

The <code>.addListenerForSingleValueEvent()</code> listener is important, as it stops any future data changes (such as the current update!) triggering the listener again.
<pre>nestedData.addListenerForSingleValueEvent(object : ValueEventListener {
        override fun onDataChange(dataSnapshot: DataSnapshot) {
            for (postSnapshot in dataSnapshot.children) {
                val child = nestedData.child(postSnapshot.key.toString()).child("uuid")
                val existingUuid = postSnapshot.child("uuid").value
                child.setValue("*$existingUuid")
            }
        }</pre>
<h3>Deleting data</h3>
Again, deleting data is very similar to updating it. The easiest way to remove data is to simply set it to null. That's it!

For example, to wipe everything inside <code>nestedData</code>:
<pre>nestedData.setValue(null)</pre>
<h2>Web interface</h2>
The web interface for Realtime Database allows reading / modifying the stored data, managing access rules, as well as enabling backups and monitoring usage.
<h3>Data tab</h3>
This tab allows adding, editing, and deleting nodes. It's also possible to create new nested data, and is a good way to form the basic data structure before beginning programmatically adding data.
<img class="alignnone size-full wp-image-1807" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/pib8cpx.png" alt="pib8cpx" width="964" height="310" />
<h3>Rules tab</h3>
The rules tab handles security for your database. These aren't covered in this tutorial, but <a href="https://firebase.google.com/docs/database/security" target="_blank" rel="noopener">the official documentation</a> is very comprehensive.
<h3>Backups tab</h3>
This tab is only useful on Blaze or above plans, and will allow you to start a manual backup or manage existing automatic backups. These will be compressed using GZip by default.
<h3>Usage tab</h3>
This tab provides information on the recent usage of your database. It is updated every minute, and is an excellent way to get a quick overview of system load, and upgrade plans if necessary.
<img class="alignnone size-full wp-image-1812" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/vukqkat.png" alt="vukqkat" width="946" height="537" />
<h2>Conclusion</h2>
Whilst Realtime Database's node based architecture is perfect for some projects, its features pale in comparison to the more modern Cloud Firestore. As such, it's hard to recommend Realtime Database for any new projects, except those that already have a node-based data structure.

The offline data caching and syncing between all clients is as powerful as ever, but I find it hard to see any benefits over Firestore.

Previous: <a href="https://blog.jakelee.co.uk//developing-android-apps-with-firebase-cloud-firestore">Developing Android Apps With Firebase Cloud Firestore</a>

Next: <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/">Developing Android Apps With FIrebase Cloud Functions</a>