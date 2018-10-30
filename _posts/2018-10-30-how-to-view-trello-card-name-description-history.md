---
ID: 1885
post_title: >
  How To View Trello Card Name /
  Description History
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/how-to-view-trello-card-name-description-history/
published: true
post_date: 2018-10-30 19:31:16
---
Trello's card management system is an extremely widely used approach to managing smaller projects, by moving cards left to right as they progress through various stages (Needs Estimate, In Progress, In QA, etc). Having a history of actions taken is essential, as it ensures all team members are aware of the card's state at any time.

The activity log below each card contains details of the card movements between columns, comments, and assigned members. However, it misses crucial information such as the card's name and description, which can easily be accidentally edited with no undo option. To access this information, we'll have to do a (very basic!) dive into Trello's API...

<!--more-->
<h2>Get card ID</h2>
First, open the target card that you need the history for. In the URL, after <code>/c/</code> there should be an 8 character long card identifier.
For example, from the URL <code>https://trello.com/c/utrTeUcA/376-test-card</code>, we can retrieve the ID of <code>utrTeUcA</code>.
<h2>Create base URL</h2>
To access the API, put the ID identified into the following URL: <code>https://trello.com/1/cards/ID HERE/actions</code>. For example, our ID from earlier gives us <code>https://trello.com/1/cards/utrTeUcA/actions</code>.

By default, this will return a JSON array of all comments on the card, with a large amount of metadata.
<h2>Filtering results</h2>
Finally, we need to make the results more readable, and retrieve only the changes we're actually interested in.

To make it more readable, the extension <a href="https://chrome.google.com/webstore/detail/jsonview/chklaanhfefbnpoihckbnefhakgolnmc" target="_blank" rel="noopener">JSONView</a> will format the returned results in an easily readable format. Once this is installed, it will automatically apply on pages that contain just JSON content, and can be disabled when not needed.

To display only name changes, add <code>?filter=updateCard:name</code> to the end of the URL. For description changes, add <code>?filter=updateCard:description</code>. This will display the new value (under <code>card</code>), and the previous value (under <code>old</code>).
<img class="alignnone size-medium" src="https://i.imgur.com/rr2lf5U.png" width="668" height="316">

Note that this data can take a few minutes to be deleted, so for testing this approach it's best to use an existing card with changes instead of making new changes.