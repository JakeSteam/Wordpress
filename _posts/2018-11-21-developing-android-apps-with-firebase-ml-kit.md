---
ID: 2020
post_title: >
  Developing Android Apps With Firebase ML
  Kit
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/developing-android-apps-with-firebase-ml-kit/
published: true
post_date: 2018-11-21 21:43:59
---
Machine Learning is, at its core, a way of letting programs learn how to do things by example. It can be used to get a program to self-learn <a href="https://www.youtube.com/watch?v=qv6UVOQ0F44" target="_blank" rel="noopener">how to play Mario</a>, or <a href="https://www.youtube.com/watch?v=gn4nRCC9TwQ" target="_blank" rel="noopener">how to walk</a>.  In this tutorial, Firebase's Machine Learning Kit (commonly known as ML Kit) will be used to retrieve text, faces, barcodes, and objects from an image.

Firebase's API provides "models" (knowledge based on data sets) for common actions such as identifying faces or objects in images. Firebase also provides the ability to utilise custom models (via TensorFlow), but only the built-in models will be covered in this tutorial. Additionally, only on-device APIs will be utilised, as cloud APIs require paid plans.

<!--more-->

This post is part of <a href="https://blog.jakelee.co.uk//firebase/">The Complete Guide to Firebase</a>. Throughout this tutorial, the <a href="https://firebase.google.com/docs/ml-kit/" target="_blank" rel="noopener">official documentation</a> may be useful.
<h2>Implementation</h2>
As always, the entire <a href="https://github.com/JakeSteam/FirebaseReference" target="_blank" rel="noopener">Firebase Reference Project is open source</a>, and there is a <a href="https://github.com/JakeSteam/FirebaseReference/pull/8" target="_blank" rel="noopener">pull request for adding Firebase ML Kit</a> if you just want to see the code changes required. This tutorial assumes you already have <a href="https://blog.jakelee.co.uk//adding-firebase-to-an-android-project/">Firebase added to your project</a>.

In this tutorial, an image will be picked from a file selector, then analysed using ML Kit for relevant information. This image can also be provided via a video stream or a camera, but using an existing file is the simplest to demonstrate the core concepts. For each ML Kit model, a raw image and analysed screenshot is provided too.
<h3>Setting up Firebase ML Kit</h3>
First, add the ML Kit library to your app-level <code>build.gradle</code>:
<pre class="prettyprint"><span class="pln">implementation </span><span class="str">'com.google.firebase:firebase-ml-vision:18.0.1'</span></pre>
Next, add the models you'll be using to your <code>AndroidManifest.xml</code> as a <code>meta-data</code> entry. This allows your app to automatically download the model when your app is installed, instead of waiting until they are actually needed. The following example includes all models used in this tutorial:
<pre>&lt;meta-data
    android:name="com.google.firebase.ml.vision.DEPENDENCIES"
    android:value="ocr,face,barcode,label" /&gt;</pre>
<h3>Obtaining a target picture</h3>
For the example app, we first define a request code for each of the 4 potential ML Kit actions to be performed (Text, Face, Barcode, or Object). These can be any number under 65535, and are used to distinguish requests:
<pre>private val TEXT_RESPONSE = 3331
private val FACE_RESPONSE = 3442
private val BARCODE_RESPONSE = 4443
private val LABEL_RESPONSE = 4143</pre>
Next, a button is created for each ML Kit identifying API demonstrated (Text, Face, Barcode, Object). <a href="https://github.com/JakeSteam/FirebaseReference/blob/284fb6a0bded83d9972f907b24e45fcd7fbf4dd4/app/src/main/res/layout/fragment_develop_ml_kit.xml">Example XML is available</a>, but the layout doesn't matter. Each of these buttons then has an <code>onClickListener</code> set, which opens the system's file picker, filtered to images only:
<pre>textButton.setOnClickListener {
    startActivityForResult(
        Intent(Intent.ACTION_GET_CONTENT).setType("image/*"), TEXT_RESPONSE)
}
faceButton.setOnClickListener {
    startActivityForResult(
            Intent(Intent.ACTION_GET_CONTENT).setType("image/*"), FACE_RESPONSE)
}
barcodeButton.setOnClickListener {
    startActivityForResult(
            Intent(Intent.ACTION_GET_CONTENT).setType("image/*"), BARCODE_RESPONSE)
}
objectButton.setOnClickListener {
    startActivityForResult(
            Intent(Intent.ACTION_GET_CONTENT).setType("image/*"), LABEL_RESPONSE)
}</pre>
Once the user picks an image, <code>onActivityResult</code> is then called. Once the <code>resultCode</code> has been checked, an Uri of the image can be created using <code>Uri.parse(data!!.dataString)</code>. This Uri can then be passed to <code>FirebaseVisionImage.fromFilePath(context, uri)</code> to obtain the image in the required format.

This image (after updating the layout's preview) is then passed to the appropriate function based on the desired ML Kit operation to be performed:
<pre>override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
    super.onActivityResult(requestCode, resultCode, data)
    if (resultCode != RESULT_OK) return
    val uri = Uri.parse(data!!.dataString)
    val image = FirebaseVisionImage.fromFilePath(activity!!, uri)
    imagePreview.setImageBitmap(image.bitmapForDebugging)
    output.text = ""
    when (requestCode) {
        TEXT_RESPONSE -&gt; retrieveText(image)
        FACE_RESPONSE -&gt; retrieveFace(image)
        BARCODE_RESPONSE -&gt; retrieveBarcode(image)
        LABEL_RESPONSE -&gt; retrieveLabels(image)
    }
}</pre>
<h3>Using ML Kit on target picture</h3>
For all of the following code examples, <code>image</code> is the <code>FirebaseVisionImage</code> passed from <code>onActivityResult</code>.
<h4>Retrieving text from an image</h4>
First, make sure <code>ocr</code> is in your <code>DEPENDENCIES</code> meta-data in your <code>AndroidManifest.xml</code>.

Next, calling <code>firebase.onDeviceTextRecognizer.processImage(image)</code> performs an OCR task using ML Kit. The added success listener contains a <code>FirebaseVisionText</code> object. This object (and all text objects below it) all have a <code>.text</code> available that will provide the extracted text. Additionally, all child objects have <code>boundingBox</code> / <code>cornerPoints</code> (the area scanned for this object), <code>confidence</code> (certainty in the text result), and <code>recognizedLanguages</code> (the identified languages).

Inside this overall summary object, there are blocks containing lines containing words containing letters. The following function loops through every word to create a count of all blocks, lines, words, and letters.
<pre>private fun retrieveText(image: FirebaseVisionImage) {
    FirebaseVision.getInstance()
            .onDeviceTextRecognizer
            .processImage(image)
            .addOnSuccessListener { textObject -&gt;
                var blocks = 0
                var lines = 0
                var words = 0
                val letters = textObject.textBlocks.sumBy { block -&gt;
                    blocks += 1
                    block.lines.sumBy { line -&gt;
                        lines += 1
                        line.elements.sumBy { word -&gt;
                            words += 1
                            word.text.length
                        }
                    }
                }
                output.text = String.format(getString(R.string.mlkit_text_data),
                        blocks, lines, words, letters, textObject.text)
            }
            .addOnFailureListener { output.text = it.localizedMessage }
}</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/text.jpg"><img class="alignleft wp-image-2027" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/text-300x171.jpg" alt="" width="400" height="228" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/textscreenshot.png"><img class="size-medium wp-image-2028 alignleft" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/textscreenshot-146x300.png" alt="" width="146" height="300" /></a>
<h4>Detecting faces in an image</h4>
First, make sure <code>face</code> is included in your <code>DEPENDENCIES</code> meta-data inside <code>AndroidManifest.xml</code> and the face detection dependency has been added alongside core ML Kit:
<pre>implementation 'com.google.firebase:firebase-ml-vision:18.0.1'
implementation 'com.google.firebase:firebase-ml-vision-face-model:17.0.2'</pre>
Next, a <code>FirebaseVisionFaceDetectorOptions</code> needs to be built. There are 5 toggleable options within:
<ul>
 	<li><code>setPerformanceMode()</code> can be <code>FAST</code> (default) or <code>ACCURATE</code>. Accurate is useful for static image analysis.</li>
 	<li><code>setLandmarkMode()</code> can be <code>NO_LANDMARKS</code> (default) or <code>ALL_LANDMARKS</code>. This feature is useful when the position of specific facial features are needed.</li>
 	<li><code>setContourMode()</code> can be <code>NO_CONTOURS</code> (default) or <code>ALL_CONTOURS</code>. This feature is useful for getting the outlines of specific facial features.</li>
 	<li><code>setClassificationMode()</code> can be <code>NO_CLASSIFICATIONS</code> (default) or <code>ALL_CLASSIFICATIONS</code>. This feature is useful when information such as likelihood of smiling / eyes being open is needed.</li>
 	<li><code>enableTracking()</code> enabled assigning IDs to faces. When enabled, this identifies users across multiple images.</li>
</ul>
Contours and face tracking cannot currently both be enabled at the same time. As such, for this example we'll enable everything except contours:
<pre>val options = FirebaseVisionFaceDetectorOptions.Builder()
        .setPerformanceMode(FirebaseVisionFaceDetectorOptions.ACCURATE)
        .setLandmarkMode(FirebaseVisionFaceDetectorOptions.ALL_LANDMARKS)
        .setClassificationMode(FirebaseVisionFaceDetectorOptions.ALL_CLASSIFICATIONS)
        .enableTracking()
        .build()</pre>
These options are then passed to <code>firebase.getVisionFaceDetector(options)</code> and <code>.detectInImage(image)</code> is called. An (potentially empty) array of faces is returned, each of which contains information on the face's ID, head rotation, body part positions, etc. Probabilities (eyes open, smiling) are given between 0 and 1, so multiply by 100 to get a confidence percentage. <code>headEulerAngleY</code> refers to the angle the head is looking left or right. 0 degrees is looking directly at the camera, negative values are looking to the left, positive values to the right (from the detected face's perspective).
<pre>FirebaseVision.getInstance()
        .getVisionFaceDetector(options)
        .detectInImage(image)
        .addOnSuccessListener { faces -&gt;
            if (faces.isEmpty()) {
                output.text = getString(R.string.mlkit_no_faces)
            } else {
                var text = ""
                faces.forEach {
                    text += String.format(getString(R.string.mlkit_face_data),
                            it.trackingId,
                            it.leftEyeOpenProbability * 100,
                            it.rightEyeOpenProbability * 100,
                            it.smilingProbability * 100,
                            it.headEulerAngleY,
                            it.headEulerAngleZ)
                }
                output.text = text
            }
        }
        .addOnFailureListener { output.text = it.localizedMessage }</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/face.jpg"><img class="alignleft wp-image-2030 " src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/face-300x169.jpg" alt="" width="396" height="223" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/facescreenshot.png"><img class="alignleft wp-image-2031 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/facescreenshot-146x300.png" alt="" width="146" height="300" /></a>
<h4>Identifying barcodes in an image</h4>
As usual, make sure <code>barcode</code> is in the <code>android:value</code> of your <code>DEPENDENCIES</code> in <code>Android.Manifest.xml</code>:
<pre>&lt;meta-data
    android:name="com.google.firebase.ml.vision.DEPENDENCIES"
    android:value="barcode" /&gt;</pre>
Then, create a <code>FirebaseVisionBarcodeDetectorOptions</code> object with all the barcode types wanted. QR Code, UPC, and EAN are the 3 most common formats.
<pre>val options = FirebaseVisionBarcodeDetectorOptions.Builder()
        .setBarcodeFormats(
                FirebaseVisionBarcode.FORMAT_QR_CODE,
                FirebaseVisionBarcode.FORMAT_AZTEC,
                FirebaseVisionBarcode.FORMAT_UPC_A,
                FirebaseVisionBarcode.FORMAT_UPC_E,
                FirebaseVisionBarcode.FORMAT_EAN_13)
        .build()</pre>
Next, pass this to <code>firebase.getVisionBarcodeDetector(options)</code> and call <code>detectInImage(image)</code>. This will return a list of barcodes detected, each of which contains the raw text, the computed value, and the type. For example, a <span style="color: #222222; font-family: Monaco, Consolas, Andale Mono, DejaVu Sans Mono, monospace;"><span style="font-size: 15px; background-color: #e9ebec;">TYPE_GEO</span></span> QR code will have a <code>rawText</code> of <code>geo:40.1234,75.1234,100</code>,  a type of <code>TYPE_GEO</code>, and a <code>geoPoint</code> object with <code>lat</code> and <code>lng</code> doubles. Using the raw text is generally safest, as all data types resolve to a sensible plain text.
<pre>FirebaseVision.getInstance()
        .getVisionBarcodeDetector(options)
        .detectInImage(image)
        .addOnSuccessListener { barcodes -&gt;
            if (barcodes.isEmpty()) {
                output.text = getString(R.string.mlkit_no_barcode)
            } else {
                var string = ""
                barcodes.forEach {
                    string += String.format(getString(R.string.mlkit_barcode_data),
                            it.rawValue,
                            getBarcodeType(it.valueType))
                }
                output.text = string
            }
        }
        .addOnFailureListener { output.text = it.localizedMessage }</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/barcode.jpg"><img class="alignleft size-medium wp-image-2033" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/barcode-300x225.jpg" alt="" width="300" height="225" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/barcodescreenshot.png"><img class="alignleft size-medium wp-image-2034" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/barcodescreenshot-146x300.png" alt="" width="146" height="300" /></a>
<h4>Labelling all objects in an image</h4>
The on-device version of this API returns the most common 400+ items, whereas the paid cloud API uses 10,000+ items. However, the local API is still very powerful, and easily detects surroundings and objects.

As always, first add <code>label</code> to your <code>DEPENDENCIES</code> inside your <code>AndroidManifest.xml</code>. Then add the image labelling library to your app-level <code>build.gradle</code>:
<pre>implementation 'com.google.firebase:firebase-ml-vision:18.0.1'
implementation 'com.google.firebase:firebase-ml-vision-image-label-model:17.0.2'</pre>
The only option for <code>FirebaseVisionLabelDetector</code> is the minimum confidence (0 to 1) threshold to display a result, and won't be used in this example. Calling <code>firebase.visionLabelDetector.detectInImage(image)</code> will return a list of <code>FirebaseVisionLabel</code>. Each of these contains a <code>label</code> (the detected object's name), <code>confidence</code> (the confidence that the object has been detected), and an <code>entityId</code> (a unique ID of the object).
<pre>private fun retrieveLabels(image: FirebaseVisionImage) {
    FirebaseVision.getInstance()
            .visionLabelDetector
            .detectInImage(image)
            .addOnSuccessListener { labels -&gt;
                if (labels.isEmpty()) {
                    output.text = getString(R.string.mlkit_no_label)
                } else {
                    var string = ""
                    labels.forEach {
                        string += String.format(getString(R.string.mlkit_label_data),
                                it.label, it.confidence * 100)
                    }
                    output.text = string
                }
            }
            .addOnFailureListener { output.text = it.localizedMessage }
}</pre>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/object.jpg"><img class="alignleft size-medium wp-image-2035" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/object-169x300.jpg" alt="" width="169" height="300" /></a> <a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/objectscreenshot.png"><img class="alignleft size-medium wp-image-2036" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/objectscreenshot-146x300.png" alt="" width="146" height="300" /></a>

&nbsp;
<h2>Web interface</h2>
The <a href="https://console.firebase.google.com/u/0/project/_/ml/apis">APIs tab of the web interface</a> shows all APIs currently in use by your apps, as well as which package name is utilising it.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/mlkitapis.png"><img class="size-full wp-image-2038 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/11/mlkitapis.png" alt="" width="983" height="317" /></a>
<h2>Conclusion</h2>
Firebase's ML Kit provides an excellent introduction to the power of machine learning assisted image analysis. Whilst the OCR, object identification, barcode reading, and facial recognition are just examples, the models are extremely powerful already. Considering the free and offline nature of these models, they are perfect for almost any implementation.

However, the very nature of the image analysis technique means that there is no debug information available. The high success rate of the models should prevent this being an issue, but the lack of information is worth keeping in mind.

From my own experimentation, the facial analysing performed amazingly well, as did the object recognition. However, somewhat surprisingly, the text identification was much less successful. This is likely due to the large number of fonts available, as well as the similarity between many letters, but it's still unusual that the most common use case is the least accurate!

Previous: <a href="https://blog.jakelee.co.uk/developing-android-apps-with-firebase-cloud-functions/">Developing Android Apps With Firebase Cloud Functions</a>