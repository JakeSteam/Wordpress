---
ID: 2244
post_title: 'How to get a &#8220;Verified&#8221; tag on your GitHub organization&#8217;s page'
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-get-a-verified-tag-on-your-github-organizations-page/
published: true
post_date: 2018-12-21 17:00:32
---
Your company's GitHub profile is a great way to show off some of the high quality work produced internally, and attract new potential developers. Additionally, all the usual benefits of sharing your code with others apply, and you'll be helping the open source community!

However without a verified domain there's no way for any visitors to know that you are who you say you are. To resolve this issue, GitHub <a href="https://blog.github.com/changelog/2018-08-07-domain-verification/" target="_blank" rel="noopener">recently introduced</a> organization domain verification. The verification process only takes a few minutes, so is definitely worth doing if your organization has an associated domain!

To complete this tutorial you'll need Owner permissions for the GitHub organization, and access to your company's DNS records. Note that completing domain verification will notify all other Owners of the organization. There is also an <a href="https://help.github.com/articles/verifying-your-organization-s-domain/" target="_blank" rel="noopener">official GitHub tutorial</a>.

<!--more-->
<h2>Adding organization email / domain</h2>
<ol>
 	<li>First, go to your organization's page.</li>
 	<li>Next, click the "Settings" tab.</li>
 	<li>You can now add a public email and a URL, these must both be verified so use the same domain if possible.</li>
 	<li>Save the changes.</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/settings.png"><img class="aligncenter size-full wp-image-2246" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/settings.png" alt="" width="720" height="505" /></a>

Your URL will now be shown on your organization's profile, but without the pretty verified tag!
<h2>Verifying domain</h2>
Now go to the "Verified domains" section on the Settings sidebar. We have to verify that we own the domains we listed on our profile in the earlier step.
<ol>
 	<li>Click "Add a domain".</li>
 	<li>Enter your URL. Note that <code>www.example.com</code> and <code>example.com</code> are different, so use the raw domain (<code>example.com</code>).</li>
 	<li>You'll be taken to a page with information on the DNS record you need to add. The verification code is unique and expires in 7 days.</li>
</ol>
<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/new.png"><img class="aligncenter size-full wp-image-2250" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/new.png" alt="" width="771" height="420" /></a>

Note that the TXT record format is: <code>_github-challenge-&lt;your organization name&gt;.&lt;your domain&gt;</code>.
<h2>Adding DNS record</h2>
Next, in a new tab you need to go to whoever handles your DNS, in my case Cloudflare. Under the DNS tab, add the TXT record from the previous step.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cloudflare.png"><img class="aligncenter size-full wp-image-2251" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cloudflare.png" alt="" width="1037" height="278" /></a>Note that once the TXT domain is added your domain suffix will disappear, this is normal!

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cloudflare2.png"><img class="aligncenter size-full wp-image-2252" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/cloudflare2.png" alt="" width="1023" height="112" /></a>
<h2>Verifying</h2>
Go back to your GitHub domain verification tab (or renavigate to it), and click the "Verify" button. Whilst DNS propagation can take up to 72 hours, with a service like Cloudflare this is almost instant.

Congratulations, your domain is now verified! Developers using your libraries are now certain that they are yours.

In addition to the verified tag, you can now view your member's email addresses (so long as they are at your domain). For example, if a user has multiple emails registered, and one of them is at your domain, you'll now be able to see that email!

Note that all members with "Owner" role are emailed to notify them of this change:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/email.png"><img class="aligncenter size-full wp-image-2253" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/email.png" alt="" width="594" height="495" /></a>

&nbsp;