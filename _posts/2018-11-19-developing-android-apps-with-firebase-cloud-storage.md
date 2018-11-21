---
ID: 1986
post_title: >
  Developing Android Apps With Firebase
  Cloud Storage
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-storage/
published: true
post_date: 2018-11-19 10:14:08
---
Firebase Cloud Storage provides an easy way to store user's files, or provide existing files to the user. Additionally, heavily customisable access control is included, and all files can be browsed via a web interface. In this tutorial's implementation, the user will be able to download sample files, and upload / delete their own arbitrary files.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, you'll need access to the <a href="https://console.firebase.google.com/u/0/project/_/storage" target="_blank" rel="noopener">Firebase Cloud Storage dashboard</a>, and the <a href="https://firebase.google.com/docs/storage/" target="_blank" rel="noopener">official documentation</a> may be useful too.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/6" target="_blank" rel="noopener">pull request for adding Firebase Cloud Storage</a> if you just want to see the code changes required.

This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.
<h3>Setting up Firebase Cloud Storage environment</h3>
Visiting the <a href="https://console.firebase.google.com/u/0/project/_/storage">Cloud Storage dashboard</a> and clicking "Get Started" will display a dialog confirming the default security rules. These rules state that any logged-in users can read and write any files, a relatively lax default ruleset! Once these default rules have been agreed to, you'll have an empty storage "bucket", ready to fill up with files.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/nofiles.png"><img class="aligncenter size-full wp-image-1989" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/nofiles.png" alt="" width="971" height="199" /></a>

Navigate to <a href="https://console.firebase.google.com/u/0/project/_/storage/rules">the rules tab</a>, and the default rules from earlier will be visible. Whilst these rules can be <a href="https://firebase.google.com/docs/storage/security/start">intricately applied</a>, in this use case 2 directories will be configured; one non-writable (<code><span style="font-family: 'courier new', courier, monospace;">sample</span></code>) and one writable (<code><span style="font-family: 'courier new', courier, monospace;">userFolder</span></code>). The following config is very straightforward, and configures the 2 directories and all subdirectories / files inside them. Additionally, a maximum filesize of 5MB (5 * 1024 * 1024 bytes) will be implemented to prevent excessive storage usage. The latter part of the conditional statement (<code>|| request.resource == null</code>) is to allow file deletion.
<pre>service firebase.storage {
  match /b/{bucket}/o {
    match /sample/{allPaths=**} {
      allow read;
    }
    match /userFolder/{allPaths=**} {
      allow read;
      allow write: if request.resource.size &lt; 5 * 1024 * 1024 || request.resource == null
    }
  }
}</pre>
Finally, to include Cloud Storage in your project, add the following to your app-level build.gradle. If you are prompted to update the library's version number, do so.
<pre>implementation 'com.google.firebase:firebase-storage:16.0.4'</pre>
<h3>Obtaining a file reference</h3>
For all of the following tasks, first a file / directory reference will be needed. This is retrieved by:
<ol>
 	<li>Getting a Cloud Storage instance with <code>storage = FirebaseStorage.getInstance()</code></li>
 	<li>Getting a reference with <code>reference = storage.child(folderName)</code></li>
</ol>
Now, any delete / download operations can be performed on that reference.
<h3>Retrieving file metadata from Cloud Storage</h3>
On your file reference, call <code>.metadata</code>, followed by <code>.addOnSuccessListener</code> and <code>.addOnFailureListener</code>:
<pre>private fun viewMetadata(reference: StorageReference) = reference.metadata
        .addOnSuccessListener {
            Toast.makeText(activity, "${it.name} has a size of ${it.sizeBytes} bytes, and is a ${it.contentType}", Toast.LENGTH_SHORT).show()
        }
        .addOnFailureListener {
            Toast.makeText(activity, "Failed to load metadata: ${it.localizedMessage}", Toast.LENGTH_SHORT).show()
        }</pre>
The <code>StorageMetadata</code> object returned on successful metadata retrieval contains a lot of useful information by default, and can be improved by adding custom metadata fields. It contains the name (<code>.name</code>), size (<code>.sizeBytes</code>), type (<code>.contentType</code>), MD5 hash (<code>.md5Hash</code>) and <a href="https://firebase.google.com/docs/reference/android/com/google/firebase/storage/StorageMetadata.html">much more</a>.
<h3>Downloading a file from Cloud Storage</h3>
First, create a local file to save the remote file into. For example, to create a temporary file with the remote file's name prefixed by <code>download_</code>, use <code>File.createTempFile("download_", reference.name)</code>.

The remote file can then be copied into the local file using <code>reference.getFile(file)</code>. Additionally, call <code>.addOnSuccessListener</code> and <code>.addOnFailureListener</code>, to end up with the following:
<pre>private fun downloadFile(reference: StorageReference) {
    val file = File.createTempFile("download_", reference.name)
    reference.getFile(file).addOnSuccessListener {
        Toast.makeText(activity, "Saved  ${reference.name} (${it.totalByteCount}bytes) to ${file.absolutePath}!", Toast.LENGTH_LONG).show()
    }.addOnFailureListener {
        Toast.makeText(activity, "Failed to download file: ${it.localizedMessage}", Toast.LENGTH_SHORT).show()
    }
}</pre>
<h3>Uploading a file to Cloud Storage</h3>
To display a system dialog offering selection of any type of file (<code>*/*</code>), the following will be used:
<pre>uploadButton.setOnClickListener {
    startActivityForResult(
            Intent(Intent.ACTION_GET_CONTENT).setType("*/*"), 1234)
}</pre>
When the file selector is closed, <code>onActivityResult</code> will be called. Inside this, the actual upload will take place.

First, check a file was successfully selected, by exiting if the <code>resultCode != RESULT_OK</code>. Afterwards, convert the file path string into a Uri, and take the last part to use as a filename.

Next, create a reference to your target file's location. In this example, <code>getUserDirectory</code> gets a unique bucket for each phone model, to avoid everyone using the same storage bucket. A reference is then created in this bucket using <code>.child(name)</code>.

Finally, <code>.putFile()</code> uploads the actual file, with <code>.addOnSuccessListener</code> and <code>.addOnFailureListener</code> used as callbacks.
<pre>override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    if (resultCode != RESULT_OK) return
    val uri = Uri.fromFile(File(data!!.dataString))
    val name = uri.lastPathSegment
    Toast.makeText(activity, "Beginning to upload $name", Toast.LENGTH_SHORT).show()
    getUserDirectory(FirebaseStorage.getInstance()).child(name).putFile(Uri.parse(data.dataString))
            .addOnSuccessListener {
                Toast.makeText(activity, "Successfully uploaded $name!", Toast.LENGTH_SHORT).show()
                addUserFile(uri.lastPathSegment)
                updateUserTable(FirebaseStorage.getInstance())
            }
            .addOnFailureListener {
                Toast.makeText(activity, "Failed to upload $name!", Toast.LENGTH_SHORT).show()
            }
    super.onActivityResult(requestCode, resultCode, data)
}</pre>
The <code>addUserFile</code> and <code>updateUserTable</code> functions in the above example can be ignored, as they are used to display files to the user in the example app.
<h3>Deleting a file from Cloud Storage</h3>
Deleting a file is extremely simple, just requiring calling <code>.delete()</code> on the file reference, along with <code>.addOnSuccessListener</code> and <code>.addOnFailureListener</code>. As with file upload, both <code>deleteUserFile</code> and <code>updateUserTable</code> are only used for the example app and can be safely ignored!
<pre>private fun deleteFile(reference: StorageReference) = reference
        .delete()
        .addOnSuccessListener {
            Toast.makeText(activity, "Deleted  ${reference.name}!", Toast.LENGTH_SHORT).show()
            deleteUserFile(reference.name)
            updateUserTable(FirebaseStorage.getInstance())
        }.addOnFailureListener {
            Toast.makeText(activity, "Failed to delete file: ${it.localizedMessage}", Toast.LENGTH_SHORT).show()
        }</pre>
<h2>Web interface</h2>
<h3>Files</h3>
The files tab can be very useful when debugging. It provides a traditional view of all folders, subfolders, and files in your Cloud Storage bucket. Whilst you cannot rename files / folders, you can delete them and view file previews / metadata. Additionally, you can create new folders and upload files manually.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/filestab.png"><img class="aligncenter size-full wp-image-1993" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/filestab.png" alt="" width="961" height="673" /></a>
<h3>Rules</h3>
The rules tab displays your current configuration, as well as letting you test it by sending fake requests. These fake requests can be used to check if certain sections of your users have the correct permissions for various files. The fake request payload is also displayed, so you can manually enter it in request execution programs like <a href="https://www.getpostman.com/apps" target="_blank" rel="noopener">Postman</a>.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/rulestab.png"><img class="aligncenter size-large wp-image-1994" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/rulestab-1024x416.png" alt="" width="730" height="297" /></a>
<h3>Usage</h3>
The usage tab shows a summary of your storage space used, number of files, bandwidth used, and total requests made. There are <a href="https://cloud.google.com/storage/quotas" target="_blank" rel="noopener">quotas for these metrics</a>, but they are fairly generous, even for the free plan:
<ul>
 	<li>Storage space used: 5TB</li>
 	<li>Number of files: Unknown</li>
 	<li>Bandwidth used: 1GB/day</li>
 	<li>Total requests: 50,000 downloads &amp; 20,000 uploads/day</li>
</ul>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/usagetab.png"><img class="aligncenter size-full wp-image-1995" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/usagetab.png" alt="" width="960" height="492" /></a>
<h2>Conclusion</h2>
Firebase's Cloud Storage provides an extremely easy to use way to allow users to host files, for free. Due to this, and the quite generous 5TB total storage limit, it is a serious contender for any use cases that include user files. For example, backing up user's data to the cloud, or sharing images in a chat program.

The 1GB/day bandwidth limit may be an issue for applications that need to transfer large files often, but should be enough for smaller implementations. Of course, since it runs on Google Cloud Platform, these limits can be scaled up to suit any use case... <a href="https://firebase.google.com/pricing/" target="_blank" rel="noopener">for a cost</a>!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/quota.png"><img class="aligncenter size-large wp-image-1997" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/quota-1024x224.png" alt="" width="730" height="160" /></a>

Previous: <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/">Developing Android Apps With Firebase Cloud Functions</a>

Next: <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-ml-kit/">Developing Android Apps With Firebase ML Kit</a>