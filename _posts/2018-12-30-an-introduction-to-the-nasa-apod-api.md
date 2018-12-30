---
ID: 2305
post_title: An introduction to the NASA APOD API
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/an-introduction-to-the-nasa-apod-api/
published: true
post_date: 2018-12-30 18:00:47
---
NASA's <a href="https://api.nasa.gov/api.html">Astronomy Picture of the Day API</a> is an amazing service providing free, reliably sourced space images. However, the official documentation can be a little lacking, hopefully this post will improve on it somewhat.

I'm currently using the API to <a href="https://github.com/JakeSteam/APODWallpaper">create an Android app that changes your wallpaper everyday</a>, so all lessons here were learnt through trial and error. The app isn't quite finished yet, but already has more features and reliability than all other similar offerings!

<!--more-->
<h2>Postman collection</h2>
Every request made in this tutorial is available in a Postman collection, so you can start using the API instantly. You'll need to <a href="http://blog.getpostman.com/2014/02/20/using-variables-inside-postman-and-collection-runner/" target="_blank" rel="noopener">create an environment variable</a> called <code>api_key</code> with your API key.

To use the collection, <a href="https://pastebin.com/raw/nwvEaHHN">save this link</a> as <code>postman.json</code>, then import it into your Postman collections:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/GMmn5ah.png"><img class="aligncenter wp-image-2307 size-medium" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/GMmn5ah-300x190.png" alt="" width="300" height="190" /></a>
<h2>Request</h2>
To return an APOD, make a <code>GET</code> request to <code>https://api.nasa.gov/planetary/apod</code>. Whilst the following 4 URL parameters are optional, your requests will almost certainly need a couple of them:
<ul>
 	<li><code>api_key</code>: Using an API key allows you to make up to 1000 requests per hour. If you do not set one, you'll hit quota limits pretty quickly. You can get one by filling in the <a href="https://api.nasa.gov/index.html#apply-for-an-api-key" target="_blank" rel="noopener">simple API key form</a>, you'll then be emailed an API key within a few minutes.</li>
 	<li><code>date</code>: This is the date you want to return the APOD for (in <code>YYYY-MM-DD</code> format), if none is set the latest APOD will be returned. Please see the "Limitations" section for more information on this.</li>
 	<li><code>start_date</code> and <code>end_date</code>: This is an alternative to <code>date</code>, and returns an array of all APODs inside the range.</li>
 	<li><code>hd</code>: This specifies whether the image's HD URL should be returned alongside a regular quality URL, and should be set to <code>true</code> or <code>false</code>. However, the API doesn't seem to always follow this parameter, and sometimes returns the HD URL even if you haven't asked for it!</li>
</ul>
A fully populated example URL is <code>https://api.nasa.gov/planetary/apod?api_key=xxxxxxxxxxxxxxxxxxxxxxxx&amp;date=2018-12-28</code>, the response for this will be described below.
<h2>Response</h2>
The response is a JSON object (or if using a date range, an array of objects), with some fields always being present and others dependant on the APOD requested. All fields are strings. for example:
<pre>{
   "copyright":"island universe",
   "date":"2018-12-28",
   "explanation":"Barred spiral galaxy NGC 1365 is truly a majestic island universe some 200,000 light-years across. Located a mere 60 million light-years away toward the chemical constellation Fornax, NGC 1365 is a dominant member of the well-studied Fornax galaxy cluster. This impressively sharp color image shows intense star forming regions at the ends of the bar and along the spiral arms, and details of dust lanes cutting across the galaxy's bright core. At the core lies a supermassive black hole. Astronomers think NGC 1365's prominent bar plays a crucial role in the galaxy's evolution, drawing gas and dust into a star-forming maelstrom and ultimately feeding material into the central black hole.",
   "hdurl":"https://apod.nasa.gov/apod/image/1812/NGC1365_HaLRGBpugh.jpg",
   "media_type":"image",
   "service_version":"v1",
   "title":"NGC 1365: Majestic Island Universe",
   "url":"https://apod.nasa.gov/apod/image/1812/NGC1365_HaLRGBpugh1024.jpg"
}</pre>
<ul>
 	<li><code>copyright</code> (optional): This field (when present) contains the copyright holder of the image. If the field is not present, the image is public domain.</li>
 	<li><code>date</code>: This field just returns the date passed in the URL, so is only useful if not defining a target APOD date.</li>
 	<li><code>explanation</code>: A text description of the photo. This usually contains 100-200 words.</li>
 	<li><code>hdurl</code> (optional): If requested, this field contains the full quality version of the image. Note that this can be missing, even if you request it!</li>
 	<li><code>media_type</code>: This field determines the type of content. Usually this is <code>image</code>, but can be <code>video</code>. There may be other <code>media_type</code>s, but I haven't found any.</li>
 	<li><code>service_version</code>: This has always been <code>v1</code>, but.. could one day change.</li>
 	<li><code>title</code>: This APOD title is usually 3-6 words long, and is reliably concise, factual, and descriptive.</li>
 	<li><code>url</code>: This field contains the actual APOD URL. Usually a <code>.jpg</code>, but for non-image APODs may be a YouTube video or another arbitrary URL.</li>
</ul>
<h2>Limitations</h2>
Whilst the API is an excellent service, it definitely isn't perfect. The following few sections are things that may be unclear initially, but need to be considered when using the API.
<h3>Reliability</h3>
The API quite often simply doesn't work. It'll return a (HTML) server error page from their CDN after a long delay (20-30 seconds). This usually persists for 5-10 minutes, the API then performs normally again. Luckily, these failed requests don't seem to use up your quota, but you'll still need to handle this unusual response.
<pre>&lt;!DOCTYPE html&gt;
&lt;html&gt;
    &lt;head&gt;
        &lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;
        &lt;meta charset="utf-8"&gt;
        &lt;title&gt;Application Error&lt;/title&gt;
        &lt;style media="screen"&gt;
		  html,body,iframe {
			margin: 0;
			padding: 0;
		  }
		  html,body {
			height: 100%;
			overflow: hidden;
		  }
		  iframe {
			width: 100%;
			height: 100%;
			border: 0;
		  }
		&lt;/style&gt;
    &lt;/head&gt;
    &lt;body&gt;
        &lt;iframe src="//www.herokucdn.com/error-pages/application-error.html"&gt;&lt;/iframe&gt;
    &lt;/body&gt;
&lt;/html&gt;</pre>
<h3>Valid dates</h3>
According to <a href="https://apod.nasa.gov/apod/archivepix.html">the APOD archive</a>, the earliest APOD is from 1995-06-16. There are a few missing days since then, requesting any of these (e.g. 1995-06-17) will return a server error:
<pre>{
    "code": 500,
    "msg": "Internal Service Error",
    "service_version": "v1"
}</pre>
It's also not guaranteed that today's APOD will be ready at midnight, especially when considering time zones. As such, any implementation should have the ability to handle today's date not returning results, and potentially falling back to the day before.
<h3>Non-image dates</h3>
Whilst most days return image URLs, some return video URLs (or potentially other content), determined by the <code>media_type</code> field in the response. For example, 2018-12-23 is a video, and returns the following:
<pre>{
    "date": "2018-12-23",
    "explanation": "About 12 seconds into this video, something unusual happens. The Earth begins to rise.  Never seen by humans before, the rise of the Earth over the limb of the Moon occurred 50 years ago tomorrow and surprised and amazed the crew of Apollo 8. The crew immediately scrambled to take still images of the stunning vista caused by Apollo 8's orbit around the Moon. The featured video is a modern reconstruction of the event as it would have looked were it recorded with a modern movie camera. The colorful orb of our Earth stood out as a familiar icon rising above a distant and unfamiliar moonscape, the whole scene the conceptual reverse of a more familiar moonrise as seen from Earth. To many, the scene also spoke about the unity of humanity: that big blue marble -- that's us -- we all live there. The two-minute video is not time-lapse -- this is the real speed of the Earth rising through the windows of Apollo 8. Seven months and three missions later, Apollo 11 astronauts would not only circle Earth's moon, but land on it.",
    "media_type": "video",
    "service_version": "v1",
    "title": "Earthrise: A Video Reconstruction",
    "url": "https://www.youtube.com/embed/1R5QqhPq1Ik?rel=0"
}</pre>
<h3>Image size</h3>
Whilst the <code>url</code> field has reasonably sized images (usually 1000-2000px width / height), the <code>hdurl</code> field can have absolutely massive images. For example, 2018-12-02 returns <code>https://apod.nasa.gov/apod/image/1812/FairyPillar_Hubble_3857.jpg</code>, a 3857x7804 image! There's certainly larger ones out there, so HD images need to be handled carefully to avoid excessive memory usage.
<h3>Quota</h3>
Your API key has a default limit of 1000 requests per hour. Every response contains a header <code>X-RateLimit-Remaining</code> with your remaining requests, and a header <code>X-RateLimit-Limit</code> with your current quota.

If the 1000/hr quota isn't enough, NASA ask you to contact them for a higher limit, although no contact details are provided!
<h3>Headers</h3>
Every response contains the following headers, although only the <code>X-RateLimit</code> ones are likely to be useful:
<pre class="response-header-item"><span class="response-header-name">Server →</span><span class="response-header-value">openresty</span>
<span class="response-header-name">Date →</span><span class="response-header-value">Fri, 28 Dec 2018 23:51:36 GMT</span>
<span class="response-header-name">Content-Type →</span><span class="response-header-value">application/json</span>
<span class="response-header-name">Transfer-Encoding →</span><span class="response-header-value">chunked</span>
<span class="response-header-name">Connection →</span><span class="response-header-value">keep-alive</span>
<span class="response-header-name">Vary →</span><span class="response-header-value">Accept-Encoding</span>
<span class="response-header-name">X-RateLimit-Limit →</span><span class="response-header-value">1000</span>
<span class="response-header-name">X-RateLimit-Remaining →</span><span class="response-header-value">979</span>
<span class="response-header-name">Via →</span><span class="response-header-value">1.1 vegur, http/1.1 api-umbrella (ApacheTrafficServer [cMsSf ])</span>
<span class="response-header-name">Age →</span><span class="response-header-value">0</span>
<span class="response-header-name">X-Cache →</span><span class="response-header-value">MISS</span>
<span class="response-header-name">Access-Control-Allow-Origin →</span><span class="response-header-value">*</span>
<span class="response-header-name">Strict-Transport-Security →</span><span class="response-header-value">max-age=31536000; preload</span>
<span class="response-header-name">Content-Encoding →</span><span class="response-header-value">gzip</span></pre>
<h2>Summary</h2>
As mentioned at the start of this post, NASA's APOD API is an extremely useful resource, even with it's faults and quirks.

I strongly recommend you <a href="https://pastebin.com/raw/nwvEaHHN" target="_blank" rel="noopener">download the Postman collection I created</a> (save as a <code>.json</code> and import) and start playing around yourself!

&nbsp;