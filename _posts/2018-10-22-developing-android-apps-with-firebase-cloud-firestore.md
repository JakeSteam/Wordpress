---
ID: 1775
post_title: >
  Developing Android Apps With Firebase
  Cloud Firestore
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-firestore/
published: true
post_date: 2018-10-22 14:20:01
---
Cloud Firestore provides an app an invisibly syncing cloud database. This takes away a lot of the complexity of ensuring data is up to date, and has very simple methods for creating, retrieving, updating, and deleting data. It also allows syncing between multiple clients, and managing the database via a web interface or an API.

Note that this service is in beta, and is intended to replace the existing Cloud Datastore. <a href="https://cloud.google.com/datastore/docs/firestore-or-datastore" target="_blank" rel="noopener">Google provides a full comparison</a> to aid in choosing the most appropriate service.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/project/_/database" target="_blank" rel="noopener">Firebase Cloud Firestore dashboard</a>, and the <a href="https://firebase.google.com/docs/firestore/quickstart" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/3" target="_blank" rel="noopener">pull request for adding Firebase Cloud Firestore</a> if you just want to see the code changes required.

This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.
<h3>Setting up</h3>
First, open the <a href="https://console.firebase.google.com/u/0/project/_/database" target="_blank" rel="noopener">Database section of the Firebase Console</a>. Whilst the actual database service will be covered in a separate post, this post covers the beta Cloud Firestore. Click the "Create database" button on the banner at the top of the page.
<img class="alignnone size-full wp-image-1776" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/pngondh.png" alt="pngondh" width="1230" height="390" />

In the dialog that appears, start the database in <b>test mode</b>. This should never be used in production, as it allows anyone to do anything to the database! For early development though, it's sufficient. <a href="https://firebase.google.com/docs/firestore/security/get-started" target="_blank" rel="noopener">A guide is available for configuring these security rules</a>.
<img class="alignnone size-full wp-image-1777" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/g14i0o2.png" alt="g14i0o2" width="699" height="450" />

It can take 20-30 seconds for the Cloud Firestore database to start up, but once it has you'll see an empty dashboard, since we've currently got no data. That's it, the database is ready to go, now for the app!
<img class="alignnone size-full wp-image-1778" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/ptkscmb.png" alt="ptkscmb" width="671" height="353" />

In the app, just add the following dependency to begin using Cloud Firestore:
<pre>
implementation 'com.google.firebase:firebase-firestore:17.1.1'</pre>
For all of the following examples, <code>db</code> is defined as <code>FirebaseFirestore.getInstance()</code>
<h3>Getting data (<code>SELECT</code>)</h3>
There are 2 ways to get data, automatically when it changes or on demand, both will be covered here.
<h4>Automatically retrieving Firestore data</h4>
Luckily, setting up a listener for data changes is extremely straightforward, and it provides a <code>querySnapshot</code> with the latest version of all rows. This can be run on any query, but for monitoring all data the following should be used:
<pre>
        db.collection(tableName).addSnapshotListener { querySnapshot, _ -&gt;
            displayDocuments(querySnapshot!!.documents)
        }</pre>
<h4>Selectively getting Firestore data</h4>
Using <code>db.collection("MyTable")</code> returns all documents (essentially rows) within in the "MyTable" collection (table).
<pre>
db.collection(tableName)
            .orderBy("number", Query.Direction.DESCENDING)
            .limit(3)
            .get()
            .addOnCompleteListener { task -&amp;gt;
                if (task.isSuccessful) {
                    displayDocuments(task.result?.documents!!)
                } else {
                    showToast("Error getting documents: ${task.exception}")
                    tableContents.text = ""
                }
            }</pre>
As with any database, usually when you're retrieving data it's going to be with a few criteria, all of which can be combined. For example:
<ul>
 	<li><b>Checking value</b>: <code>.whereLessThan("columnName", 100)</code>, <code>.whereEqualTo("columnName", "value")</code>, or <code>.whereGreaterThanOrEqualTo("columnName", 100)</code></li>
 	<li><b>Limiting results</b>: <code>.limit(5)</code></li>
 	<li><b>Ordering</b>: <code>.orderBy("total", Query.Direction.DESCENDING)</code></li>
</ul>
<h3>Inserting data (<code>CREATE</code>)</h3>
<h4>Automatically setting Firestore ID</h4>
Creating a new row / document is extremely easy, and just requires passing a <code>HashMap</code> of columns and values to <code>.add()</code>. Adding listeners is simple too.
<pre>
db.collection(tableName)
            .add(hashMapOf(
                    "columnA" to 123,
                    "columnB" to "a value"
            ) as Map)
            .addOnSuccessListener { documentReference -&gt;
                showToast("DocumentSnapshot added with ID: ${documentReference.id}")
            }
            .addOnFailureListener { e -&gt;
                showToast("Error adding document: $e")
            }</pre>
<h4>Manually setting Firestore ID</h4>
Whilst similar to the previous section, manually setting a Firestore ID (primary key) requires looking up the non-existent ID with <code>.document()</code>, then setting it's values using <code>set()</code>.
<pre>
       db.collection(tableName).document("newDocumentID")
            .set(hashMapOf(
                    "columnA" to 123,
                    "columnB" to "a value"
            ) as Map)
            .addOnSuccessListener {
                showToast("DocumentSnapshot added with ID: $id")
            }
            .addOnFailureListener { e -&gt;
                showToast("Error writing document: $e")
            }</pre>
<h3>Updating data (<code>UPDATE</code>)</h3>
Updating rows is very straightforward, once a reference to a row is obtained <code>.set()</code> will do all the work of synchronising changes and resolving any conflicts.

The following snippets assumes <code>it.id</code> is a document reference, and updates "columnName" to "1234":
<pre>
                        val ref = db.collection(tableName).document(it.id)
                        it.data?.let {
                            ref.update("columnName", 1234)
                        }</pre>
<h3>Deleting data (<code>DELETE</code>)</h3>
Similarly to updating data, deleting data just requires calling <code>.delete()</code> on the document reference.
<pre>
                        val ref = db.collection(tableName).document(it.id)
                        ref.delete()</pre>
<h2><a href="https://console.firebase.google.com/u/0/project/_/database/firestore" target="_blank" rel="noopener">Web interface</a></h2>
<h3>Data tab</h3>
The Cloud Firestore web interface's <a href="https://console.firebase.google.com/u/0/project/_/database/firestore/rules" target="_blank" rel="noopener">data tab</a> is a convenient way of viewing all data in the database. All collections (tables), documents (rows), and fields can be added, edited, or deleted directly. This can be extremely beneficial when attempting to debug a data issue, as it allows diagnosing the issue from any device.

In addition to viewing all data, rows can be filtered using the standard selection criteria, and sorted.
<img class="alignnone size-full wp-image-1779" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/svifjqu.png" alt="svifjqu" width="1283" height="487" />
<h3>Rules tab</h3>
The <a href="https://console.firebase.google.com/u/0/project/_/database/firestore/rules" target="_blank" rel="noopener">rules tab</a> is worth visiting before publishing any app using Cloud Firestore. Similar to routing tables in a normal server, these rules can be used to configure who can read and write what data. For example, users may be able to edit fields in their row, but not in anybody else's. Assuming the configuration is still set to "Test" from earlier, Firestore will helpfully warn you that the configuration is a massive security risk.

The <a href="https://firebase.google.com/docs/firestore/security/rules-structure?authuser=0" target="_blank" rel="noopener">existing documentation for Firestore rules</a> is extremely comprehensive, and the ability to simulate all requests (even with fake user authentication) helps ensure access control is correctly setup.
<img class="alignnone size-full wp-image-1780" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/tji8k5l.png" alt="tji8k5l" width="1284" height="529" />
<h3>Indexes tab</h3>
Cloud Firestore automatically creates indexes for you, and this is usually enough to ensure solid performance. However, advanced users may wish to <a href="https://firebase.google.com/docs/firestore/query-data/indexing?authuser=0" target="_blank" rel="noopener">add custom indexes</a>. All of these manually added indexes will be displayed here, and can be edited.
<h3>Usage tab</h3>
The usage tab just provides a link to Google Cloud Platform, which reveals Cloud Firestore's <a href="https://firebase.google.com/docs/firestore/quotas?authuser=0" target="_blank" rel="noopener">very generous usage limits</a>. The table below shows the capacity provided for free, which should be enough for smaller apps.
<img class="alignnone size-full wp-image-1781" src="https://blog.jakelee.co.uk//wp-content/uploads/2018/10/qklb0fr.png" alt="qklb0fr" width="866" height="238" />

Note that there are also reasonable limits on data stored, writes, indexes, and security rules. <a href="https://console.cloud.google.com/appengine/quotadetails" target="_blank" rel="noopener">A project's current quota can be viewed on Google Cloud Platform</a>.
<h2>Conclusion</h2>
Whilst it can be a little bit scary giving up control over data persistence, syncing, and access control to a cloud-based service instead of a more typical server, it has a lot of advantages. The costs can be dramatically lower, and the setup time is next to zero. In addition, it offers more reliable scalability than Firebase's existing Datastore product, and faster feature innovation, at the cost of lacking the normal, non-beta database's proven history. Finally, the extreme ease of hooking into other Firebase services (whilst still being able to delegate to a traditional server where necessary) makes it a very attractive offering for new systems.

Another overview of Firebase Cloud Firestore is available by <a href="https://medium.com/@mono0926/firestore1-5d04cdb683bc" target="_blank" rel="noopener">@mono0926 on Medium</a>, and an <a href="https://dzone.com/articles/cloud-firestore-read-write-update-and-delete" target="_blank" rel="noopener">excellently in-depth look at querying Cloud Firestore</a> has been written by Peter Ekene Eze on DZone.

Previous: <a href="https://blog.jakelee.co.uk//developing-android-apps-with-firebase-authentication/">Developing Android Apps With Firebase Authentication</a>
Next: <a href="https://blog.jakelee.co.uk//developing-android-apps-with-firebase-realtime-database">Developing Android Apps With Firebase Realtime Database</a>