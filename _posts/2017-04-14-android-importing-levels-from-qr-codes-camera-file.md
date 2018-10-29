---
ID: 1024
post_title: >
  Importing Levels From QR Codes (Camera /
  File)
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/android-importing-levels-from-qr-codes-camera-file/
published: true
post_date: 2017-04-14 09:00:21
---
<h2>The Problem</h2>
In a <a href="https://gamedevalgorithms.com/2017/04/06/exporting-levels-into-qr-codes-using-zxing/">previous post</a>, it was discussed how to export levels from an Android game (in this case <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.cityflow" target="_blank" rel="noopener">Connect Quest</a>) so that other players could play them. Now that they're exported, we need to be able to import them again! This post will explain how to import QR codes either directly from the camera, or embedded within an image on the file system.

<!--more-->
<h2>The Solution</h2>
<h4>Camera</h4>
To avoid including a barcode reader, camera display, orientation tracking, and requiring the camera permission, we offload this responsibility to an existing app that is present on many phones, <a href="https://play.google.com/store/apps/details?id=com.google.zxing.client.android&amp;hl=en_GB" target="_blank" rel="noopener">Barcode Scanner by ZXing</a>. Whilst there is a risk of the user not having this app installed, in my specific use case it wasn't worth spending weeks reinventing the wheel for a little used feature. It is possible to embed ZXing and do the barcode scanning without a third party application.

When launching the barcode scanner, we also inform it (via the <code>SCAN_MODE</code> intent data) that we're looking for QR Codes, to avoid it looking for other unrelated formats. If the user doesn't have the application installed, we can't scan, so we redirect the user to the store page for the ZXing app, and inform them what is happening.

The following is set as the <code>onClick</code> action for an import button, the result will be received by <code>onActivityResult</code>, described later in this post.
<pre>
public void importFromCamera(View v) {
    try {
        Intent intent = new Intent("com.google.zxing.client.android.SCAN");
        intent.putExtra("SCAN_MODE", "QR_CODE_MODE");
        startActivityForResult(intent, INTENT_CAMERA);
    } catch (Exception e) {
        Toast.makeText(this, R.string.error_no_barcode_scanner, Toast.LENGTH_SHORT).show();
        Uri marketUri = Uri.parse("market://details?id=com.google.zxing.client.android");
        Intent marketIntent = new Intent(Intent.ACTION_VIEW, marketUri);
        startActivity(marketIntent);
    }
}</pre>
A video of this in action is embedded below. Note the quick scanning of the QR code, and how the data is immediately available for processing (level imported &amp; ready to play straight away).

https://www.youtube.com/watch?v=Je0qBQhsTbE
<h4>Photo</h4>
Retrieving a photo is a little more complex than live scanning a QR code, but it's done without requiring another application. First, the <code>onClick</code> method of the import button is called, and passes the permission required (<code>READ_EXTERNAL_STORAGE</code>) and work to do (<code>importFromFile()</code>) to a <code>runIfPossible</code> function.
<pre>
public void importFromFile(View v) {
    PermissionHelper.runIfPossible(Manifest.permission.READ_EXTERNAL_STORAGE, new Runnable() {
        @Override
        public void run() {
            importFromFile();
        }
    });
}</pre>
This <code>runIfPossible</code> function utilises the very straightforward <a href="https://github.com/aitorvs/allowme" target="_blank" rel="noopener">AllowMe library</a>. First, it checks if the permission is granted already. If it is, then we can run the callback (work to do) we passed through, otherwise the permission needs to be gained.
<pre>
public static void runIfPossible(final String permission, final Runnable callback) {
    if (!AllowMe.isPermissionGranted(permission)) {
        new AllowMe.Builder()
                .setPermissions(permission)
                .setCallback(new AllowMeCallback() {
                    @Override
                    public void onPermissionResult(int requestCode, PermissionResultSet result) {
                        if (result.isGranted(permission)) {
                            callback.run();
                        }
                    }
                })
                .request(123);
    } else {
        callback.run();
    }
}</pre>
To gain the permission, a standard Android permission request is displayed, where the user can choose to allow or deny the app access to external storage. If it is approved (<code>result.isGranted()</code>), then the callback is run. If they deny the permission, then nothing is done, since we don't have the access we need. Note that the request code here is irrelevant, since there are no other request codes being used in this code area.

<img class="alignnone size-full wp-image-1071" src="https://blog.jakelee.co.uk//wp-content/uploads/2017/04/hme7ygb.png" alt="hmE7YgB" width="1043" height="658" />

The <code>importFromFile</code> function that does the actual work is very simple, and just opens a native Android image picker. This allows the user to use an interface that is familiar to them to navigate their images, and eventually select one to send back to our activity.
<pre>
private void importFromFile() {
    Intent intent = new Intent();
    intent.setType("image/*");
    intent.setAction(Intent.ACTION_GET_CONTENT);
    startActivityForResult(Intent.createChooser(intent, "Select Picture"), INTENT_FILE);
}</pre>
<h4>Receiving</h4>
The activity that launched the camera or file import tasks now needs to be notified that there is incoming data. This is done via <code>onActivityResult</code>, since both of the previous tasks used <code>startActivityForResult</code>, so will return data when they are completed (either cancelled, or found an image / QR code to import).

The <code>INTENT_CAMERA</code> and <code>INTENT_FILE</code> values are just constants we used when starting the data-retrieval actions, so that we know which is returning data. They can be any integer, they should be unique though. First, we check that the action wasn't a cancellation using <code>resultCode == RESULT_OK</code>, then process the retrieved data (covered after code snippet).

If the <code>puzzleString</code> has data, and it is successfully imported (split up, sanity checked, and added to database), then perform any post-import actions required.
<pre>
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (resultCode == RESULT_OK) {
        String puzzleString = "";
        if (requestCode == INTENT_CAMERA) {
            puzzleString = data.getStringExtra("SCAN_RESULT");
        } else if (requestCode == INTENT_FILE) {
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(this.getContentResolver(), data.getData());
                puzzleString = StorageHelper.readQRImage(this, bitmap);
            } catch (Exception e) {
                AlertHelper.error(this, AlertHelper.getError(AlertHelper.Error.FILE_IMPORT_FAIL));
            }
        }

        if (!puzzleString.equals("") &amp;&amp; PuzzleShareHelper.importPuzzleString(puzzleString, false)) {
            GooglePlayHelper.UpdateEvent(Constants.EVENT_IMPORT_PUZZLE, 1);
            AlertHelper.success(this, R.string.alert_puzzle_imported);
            populatePuzzles();
        } else if (requestCode == INTENT_CAMERA) {
            AlertHelper.error(this, AlertHelper.getError(AlertHelper.Error.CAMERA_IMPORT_FAIL));
        } else if (requestCode == INTENT_FILE) {
            AlertHelper.error(this, AlertHelper.getError(AlertHelper.Error.FILE_IMPORT_FAIL));
        }
    }
}</pre>
The ZXing barcode scanner returns the data as a string extra under the key <code>SCAN_RESULT</code>, so we just read that. Easy! The file reader unfortunately has more work to do. First of all it checks the bitmap can actually be read (to avoid misnamed extensions, deleted files, private files, etc), then calls <code>readQRImage</code>.

Initially, ZXing was used for processing, however it was quite unreliable and regularly could not detect codes. As such, the <a href="https://codelabs.developers.google.com/codelabs/bar-codes/" target="_blank" rel="noopener">Google Vision library</a> was used instead. Using this library, we create a <code>BarcodeDetector</code> that is looking for QR codes, scan the image, then retrieve the first barcode found (there should only be one anyway!). The image
<pre>
public static String readQRImage(Activity activity, Bitmap bitmap) {
    String contents = "";

    BarcodeDetector barcodeDetector = new BarcodeDetector.Builder(activity)
            .setBarcodeFormats(Barcode.QR_CODE)
            .build();

    SparseArray detectedBarcodes = barcodeDetector.detect(new Frame.Builder()
            .setBitmap(bitmap)
            .build());

    if (detectedBarcodes.size() &gt; 0 &amp;&amp; detectedBarcodes.valueAt(0) != null) {
        contents = detectedBarcodes.valueAt(0).rawValue;
    }
    return contents;
}</pre>
The camera or file QR code has now been successfully read, the puzzle imported, and the user is ready to play it!
<h2>The Conclusion</h2>
Supporting user created content can be a daunting task at first, but it's worth the investment to increase engagement and allow the game a life of it's own. Of course, if the app has the server resources to create a hosted content hub, that is superior, but offline distribution like this post describes is suitable for most smaller apps.

Whilst offloading the camera barcode scanning to a third party app isn't ideal, it was decided upon as the approach to prevent having to integrate camera APIs, and to increase reliability. The file import option is also far more likely to be used, as users are more likely to save a picture from the internet than to scan a live QR code. Additionally, the user could just take a photo and scan that if they were unwilling to install another app.

Hopefully this post about how Connect Quest handles level sharing has encouraged you to consider it for your next game, and use this guide as a starting point. Good luck!

As always, there is <a href="https://gist.github.com/JakeSteam/459db1f41f1d0089530181c86faf19eb" target="_blank" rel="noopener">a Gist available</a> for all code used in this post.