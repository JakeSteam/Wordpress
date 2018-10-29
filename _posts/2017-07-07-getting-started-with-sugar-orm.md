---
ID: 1456
post_title: Getting Started with Sugar ORM
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/getting-started-with-sugar-orm/
published: true
post_date: 2017-07-07 11:40:25
---
<a href="https://github.com/chennaione/sugar" target="_blank" rel="noopener">Sugar</a> is a very easy to use ORM library used to make handling databases on Android hassle-free. Whilst it lacks some features, it is ideally suited to smaller projects due to the simple syntax.

There is <a href="http://satyan.github.io/sugar/getting-started.html" target="_blank" rel="noopener">official documentation</a>, but it misses a few key points, so this article will serve as an alternative "Getting Started" guide. It also highlights a few vital options that aren't mentioned in the official guide, and is geared towards those new to Android who want an easy way to setup a local database.<!--more-->
<h2>Setting Up</h2>
Setting up Sugar is very straightforward, and contains just three simple steps.
<h4>1. Adding the library</h4>
As with any other Gradle library, add the following to your <code>build.gradle</code> dependencies, and perform a rebuild (Android Studio will prompt to do this automatically). This will let us reference the library's functionality:
<pre>compile <span class="pl-s"><span class="pl-pds">'</span>com.github.satyan:sugar:1.5<span class="pl-pds">'</span></span></pre>
<h4>2. Connecting app to Sugar</h4>
So that Sugar can run when the application starts, and perform other tasks, it needs to be created and destroyed when the application is, not an individual activity. To do this, we need to make a new class that extends <code>Application</code>, which we can then use to initialise Sugar and other services. I generally put this file outside of any subfolders in the project structure, but it can go anywhere.
<pre>
public class MainApplication extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        SugarContext.init(this);
    }

    @Override
    public void onTerminate() {
        super.onTerminate();
        SugarContext.terminate();
    }
}</pre>
Now that we have our <code>MainApplication</code> class, we need to define it in the <code>AndroidManifest.xml</code>. To do this, just add / modify the <code>android:name=</code> attribute on <code></code> to point to <code>.MainApplication</code>. If you placed the new class in a subfolder, it will be at <code>.Subfolder.MainApplication</code>:
<pre>
&lt;application
    android:name=".MainApplication"</pre>
<h4>3. Configuring options</h4>
Final step! All that's left to do is configure a few options. These are all inside the <code>&lt;application</code> tag in <code>AndroidManifest.xml</code> in the format <code></code>:
<table dir="ltr" border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td>Name</td>
<td>Value</td>
<td>Description</td>
</tr>
<tr>
<td>DATABASE</td>
<td>yourapp.db</td>
<td>The name of your database file. This can be anything, but it's generally simpler to just use your app's name.</td>
</tr>
<tr>
<td>VERSION</td>
<td>1</td>
<td>The version of your database. Update this when you add new columns / tables to automatically update your database.</td>
</tr>
<tr>
<td>QUERY_LOG</td>
<td>false</td>
<td>Whether every query should be written to the log. This can slow down your app, but can be useful during development.</td>
</tr>
<tr>
<td>DOMAIN_PACKAGE_NAME</td>
<td>com.yourpackagename.model</td>
<td>Where your entities are stored. It's cleanest to use a subfolder that only contains these classes.</td>
</tr>
</tbody>
</table>
That's it, you're good to go! The rest of this guide will cover creating your entities / model, and basic usage.
<h2>Using Sugar</h2>
<h4>Creating entities</h4>
In this example, we'll use a simple table with just an <code>id</code> and a <code>text</code>.
<pre>
@Table(name="mytable")
public class Text extends SugarRecord {
    @Column(name = "a") private Long id;
    @Column(name = "b") private String text;

    public Text() {}

    public Text(Long id, String text) {
        this.id = id;
        this.text = text;
    }

    @Override
    public Long getId() {
        return id;
    }

    @Override
    public void setId(Long id) {
        this.id = id;
    }

    public String getText() {
        return text;
    }

    public void setText(String text) {
        this.text = text;
    }
}</pre>
The first thing to look at is the most important, the <code>extends SugarRecord</code>. This lets Sugar know that this is a database table. In this example, we've used <code>@Table(name=</code> to tell Sugar to save the table under "mytable" instead.

Next, we declare the columns we want to include, and create getters and setters for them. As before, we can use <code>@Table(name=</code> to set a custom name if desired. Note that <code>id</code> is the built-in Sugar primary key, hence the <code>@Override</code>. It can optionally be included in your schema, or handled invisibly by Sugar.

Finally, we add a constructor with all parameters, and one with none. A constructor taking no parameters is <strong>required</strong> by Sugar ORM, and the table will not be usable without it. All of the getters / setters / constructors can be created by Android Studio by pressing <code>Alt</code> + <code>Insert</code> inside the class.
<h4>Saving data</h4>
Data can be saved by creating a new instance of the object, and calling <code>.save()</code> on it. This can also be included in your constructor if auto-save is desired.
<pre>
Text text = new Text(1, "Example text");
text.setText("New test");
text.save();</pre>
<h4>Loading data</h4>
Data can be loaded from the database by id, using the query builder, or directly using SQL. All 3 of the following examples return the same result, the <code>Text</code> object with <code>id</code> 1.
<ol>
 	<li>Using primary key <code>id</code>:
<pre>Text.findById(Text.class, 1);</pre>
</li>
 	<li>Using query builder:
<pre>Select.from(Text.class)
        .where(
                Condition.prop("a").eq(1)
        ).first();</pre>
</li>
 	<li>Using SQL:
<pre>Text.find(Text.class, "a = 1").get(0);</pre>
</li>
</ol>
Note that the 2nd and 3rd methods return a <code>List</code>, and we're selecting the first record. The correct method to use depends on the situation, but looking rows up by their primary key is generally a good idea if possible for performance reasons.
<h2>Conclusion</h2>
I've personally used Sugar for a few of my projects, such as <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmith" target="_blank" rel="noopener">Pixel Blacksmith</a> and <a href="https://play.google.com/store/apps/details?id=uk.co.jakelee.blacksmithslots">Blacksmith Slots</a>, and found the simple syntax and speedy performance very helpful. However, colleagues have used it on more complex projects and found it lacking, so it may not be suitable for large scale commercial projects.

It also doesn't receive many updates, but as it is relatively bug free due to the simple nature of it, this is less of an issue. Unfortunately it seems support and repository maintenance are lacking, but I still strongly recommend it for smaller projects.

As a reminder, there is <a href="http://satyan.github.io/sugar/index.html" target="_blank" rel="noopener">official documentation</a>, and some <a href="https://github.com/chennaione/sugar" target="_blank" rel="noopener">further reading on the repository</a> which may be helpful to refer to.

All code used in this article is <a href="https://gist.github.com/JakeSteam/f813265fba2cec06be08051e304cf191" target="_blank" rel="noopener">available as a Gist</a>.