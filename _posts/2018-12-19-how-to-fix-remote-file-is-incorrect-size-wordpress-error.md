---
ID: 2227
post_title: 'How to fix &#8220;Remote file is incorrect size&#8221; WordPress error'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-fix-remote-file-is-incorrect-size-wordpress-error/
published: true
post_date: 2018-12-19 22:09:09
---
Whilst trying to <a href="https://shop.crowdfavorite.com/ramp/" target="_blank" rel="noopener">RAMP</a> posts from your staging environment to live, or import posts using <a href="https://en-gb.wordpress.org/plugins/wordpress-importer/" target="_blank" rel="noopener">WordPress Importer</a>, a lot of under-the-hood code has to be run. One of the safety checks included in this is comparing the expected size of files with the actual size. Whilst this usually passes without any issues, occasionally it can cause the transfer to fail, leading to the "Remote file is incorrect size" error.

This error is occasionally caused by a corrupted file or network issues, but in those cases trying again resolves the issue. If a retry doesn't fix it, more drastic action has to be taken! Removing the error handling code is an extreme step, so should only be taken if nothing else fixes the issue. That being said, it does instantly solve the problem with no noticeable side effects.<!--more-->
<h2><a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/broken.png"><img class="aligncenter wp-image-2228 size-large" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/broken-1024x561.png" alt="" width="730" height="400" /></a></h2>
<h2>Find your file size checker</h2>
First, we need to open the file that performs the size check inside a text editor (e.g. Notepad++).

If you are using <a href="https://en-gb.wordpress.org/plugins/wordpress-importer/" target="_blank" rel="noopener">WordPress Importer</a>, this code is in:
<pre>/wp-content/plugins/wordpress-importer/wordpress-importer.php</pre>
If you are using <a href="https://shop.crowdfavorite.com/ramp/" target="_blank" rel="noopener">RAMP</a>, this code is in the following file (on the server being RAMPed to):
<pre>/wp-content/plugins/ramp/classes/deploy.class.php</pre>
<h2>Comment out the code</h2>
Next, comment out the lines that handle file size checking. Do this by adding <code>/*</code> at the start and <code>*/</code> at the end.

If you are using <a href="https://en-gb.wordpress.org/plugins/wordpress-importer/" target="_blank" rel="noopener">WordPress Importer</a>, the code to comment out is:
<pre>if ( isset( $headers['content-length'] ) &amp;&amp; $filesize != $headers['content-length'] ) {
	@unlink( $upload['file'] );
	return new WP_Error( 'import_file_error', __('Remote file is incorrect size', 'wordpress-importer') );
}</pre>
If you are using <a href="https://shop.crowdfavorite.com/ramp/" target="_blank" rel="noopener">RAMP</a>, the code to comment out is:
<pre>$received_size = filesize($upload['file']);
if ( $file_size !== $received_size ) {
	@unlink($upload['file']);
	return new WP_Error( 'import_file_error', sprintf(__('Remote file is incorrect size: expected %1$d, got %2$d', 'wordpress-importer'), $file_size, $received_size));
}</pre>
<h2>Finishing up</h2>
Finally save your file, push it to your server, then try the importer / RAMP action again. Problem solved!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cslrcIr.png"><img class="aligncenter size-full wp-image-2229" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cslrcIr.png" alt="" width="1028" height="438" /></a>

Thanks to Codeboxr's <a href="https://codeboxr.com/fix-remote-file-is-incorrect-size-for-wordpress-import-error/" target="_blank" rel="noopener">Sabuj Kundu's article</a> for the WordPress Importer addition.