---
ID: 2359
post_title: >
  Creating a RecyclerView with multiple
  content types and layouts in Kotlin
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-recyclerview-with-multiple-content-types-and-layouts-in-kotlin/
published: true
post_date: 2019-01-29 18:00:10
---
RecyclerViews are a little bit complicated to get started with, but almost every app has one or two of them somewhere. One of the first problems you may encounter when using them is their lack of a built-in way to handle multiple content types. Creating and binding a single layout is very straightforward, but multiple layouts requires a slightly more complex setup.

This tutorial will cover setting that up, as well as providing a simple content generator to test your <code>RecyclerView</code>. This entire project, along with all layouts, is <a href="https://gist.github.com/JakeSteam/97a827d4d7ccced73fed798025f61ea1">available as a Gist</a>, or as <a href="https://github.com/JakeSteam/StickyHeaders/tree/remove-stickyheaders">a GitHub repo</a>.

<!--more-->
<h2>Setting up the RecyclerView</h2>
First, create a simple <code>RecyclerView</code> in an xml layout, e.g. <code>activity_main.xml</code>:
<pre>&lt;android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" /&gt;</pre>
Next, inside your <code>onCreate</code> (or <code>onCreateView</code>) setup your <code>RecyclerView</code>'s adapter (source of content), and layout manager (to handle the layout logic). <code>getSampleRows</code> will be covered at the end of this post, it can just be <code>listOf()</code> for now if you want a passing build.
<pre>override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    recyclerView.adapter = ContentAdapter(getSampleRows(10))
    recyclerView.layoutManager = LinearLayoutManager(this)
}</pre>
Finally, make a <code>ContentAdapter</code> class that implements the <code>RecyclerView</code> adapter:
<pre>class ContentAdapter(private val rows: List&lt;IRow&gt;) : RecyclerView.Adapter&lt;RecyclerView.ViewHolder&gt;() { }</pre>
<h2>Creating the wrapper classes</h2>
In this example, there will be 3 types of content: Header, Message, and Colour. Each of these types needs a class defined, to hold any associated information. These should all extends a base class, so that a single list of all the content types can be created. Luckily, with Kotlin class creation is incredibly succinct:
<pre>interface IRow
class HeaderRow(val date: String, val title: String) : IRow
class MessageRow(val message: String) : IRow
class ColourRow(val colour: Int) : IRow</pre>
<h2>Creating the view holder layouts</h2>
Each of these types should also have an XML layout:
<h4><code>row_colour.xml</code>:</h4>
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;ImageView
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/colour"
        android:layout_height="50dp"
        android:layout_width="match_parent" /&gt;</pre>
<h4><code>row_header.xml</code>:</h4>
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;android.support.constraint.ConstraintLayout
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="50dp"
        android:background="@android:color/black"&gt;
    &lt;TextView
            android:id="@+id/title"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            android:paddingStart="10dp"
            android:textColor="@android:color/white"
            android:textSize="18sp"/&gt;
    &lt;TextView
            android:id="@+id/date"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toEndOf="@id/title"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintHorizontal_bias="1.0"
            android:paddingEnd="10dp"
            android:textColor="@android:color/white"
            android:textSize="18sp"/&gt;
&lt;/android.support.constraint.ConstraintLayout&gt;</pre>
<h4><code>row_message.xml</code>:</h4>
<pre>&lt;?xml version="1.0" encoding="utf-8"?&gt;
&lt;android.support.constraint.ConstraintLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:padding="10dp"&gt;
    &lt;TextView 
            android:id="@+id/message" 
            android:layout_width="wrap_content" 
            android:layout_height="wrap_content" /&gt;
&lt;/android.support.constraint.ConstraintLayout&gt;</pre>
<h2>Creating the view holder classes</h2>
Each content type's ViewHolder should map the UI elements that need to be populated to a local variable that can be referenced later:
<pre>class HeaderViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    val dateView: TextView = itemView.findViewById(R.id.date)
    val titleView: TextView = itemView.findViewById(R.id.title)
}

class MessageViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    val messageView: TextView = itemView.findViewById(R.id.message)
}

class ColourViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView) {
    val colourView: ImageView = itemView.findViewById(R.id.colour)
}</pre>
Defining a constant for each type simplifies the process of creating and binding ViewHolders based on content type:
<pre>companion object {
    private const val TYPE_HEADER = 0
    private const val TYPE_MESSAGE = 1
    private const val TYPE_COLOUR = 2
}</pre>
<code>onCreateViewHolder</code> should inflate a different layout (and use a different ViewHolder) depending on the content type constant. In this case:
<pre>override fun onCreateViewHolder(parent: ViewGroup, viewType: Int) = when (viewType) {
        TYPE_HEADER -&gt; HeaderViewHolder(LayoutInflater.from(parent.context)
            .inflate(R.layout.row_header, parent, false))
        TYPE_MESSAGE -&gt; MessageViewHolder(LayoutInflater.from(parent.context)
            .inflate(R.layout.row_message, parent, false))
        TYPE_COLOUR -&gt; ColourViewHolder(LayoutInflater.from(parent.context)
            .inflate(R.layout.row_colour, parent, false))
        else -&gt; throw IllegalArgumentException()
    }</pre>
<h2>Binding the view holder</h2>
First, the type of the content has to be determined. This can be done by passing the type as a field along with each piece of content, but my preferred approach is using type checking. For example, where <code>rows</code> is the content to be displayed:
<pre>override fun getItemViewType(position: Int): Int =
    when (rows[position]) {
        is HeaderRow -&gt; TYPE_HEADER
        is MessageRow -&gt; TYPE_MESSAGE
        is ColourRow -&gt; TYPE_COLOUR
        else -&gt; throw IllegalArgumentException()
    }</pre>
Next, now that the content type has been determined, a view holder has to be passed by <code>onBindViewHolder</code> to the appropriate <code>onBind</code> function:
<pre>override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) =
    when (holder.itemViewType) {
        TYPE_HEADER -&gt; onBindHeader(holder, rows[position] as ContentAdapter.HeaderRow)
        TYPE_MESSAGE -&gt; onBindMessage(holder, rows[position] as ContentAdapter.MessageRow)
        TYPE_COLOUR -&gt; onBindColour(holder, rows[position] as ContentAdapter.ColourRow)
        else -&gt; throw IllegalArgumentException()
    }</pre>
Each of these <code>onBind</code> functions exists solely to retrieve data from the <code>row</code> item, and set it in the <code>ViewHolder</code>'s UI element classes:
<pre>private fun onBindHeader(holder: RecyclerView.ViewHolder, row: HeaderRow) {
    val headerRow = holder as HeaderViewHolder
    headerRow.titleView.text = row.title
    headerRow.dateView.text = row.date
}

private fun onBindMessage(holder: RecyclerView.ViewHolder, row: MessageRow) {
    (holder as MessageViewHolder).messageView.text = row.message
}

private fun onBindColour(holder: RecyclerView.ViewHolder, row: ColourRow) {
    (holder as ColourViewHolder).colourView.setBackgroundColor(row.colour)
}</pre>
Finally, standard RecyclerView functions also have to be implemented:
<pre>override fun getItemCount() = rows.count()</pre>
<h2>Adding example RecyclerView content</h2>
For this project, a simple random content creator was made. It generates random integers in a range, random dates in a range, random letter combinations, random UUIDs, and random colours:
<pre>class Randomiser {
    companion object {
        fun int(min: Int, max: Int): Int {
            return (min..max).shuffled().last()
        }

        fun date(): String {
            val calendar = Calendar.getInstance()
            calendar.add(Calendar.DAY_OF_YEAR, -int(0, 1000))
            val format = SimpleDateFormat("dd/MM/yyyy", Locale.UK)
            return format.format(calendar.time)
        }

        fun word() = ('a'..'z').map { it }.shuffled().subList(0, 8).joinToString("").capitalize()

        fun message() = UUID.randomUUID().toString()

        fun colour(): Int {
            val rnd = Random()
            return Color.argb(255, rnd.nextInt(256), rnd.nextInt(256), rnd.nextInt(256))
        }
    }
}</pre>
This was used to generate a random number of rows for the <code>RecyclerView</code>:
<pre>private fun getSampleRows(numSections: Int): List&lt;ContentAdapter.IRow&gt; {
    val rows = mutableListOf&lt;ContentAdapter.IRow&gt;()
    for (i in 1..numSections) {
        rows.add(ContentAdapter.HeaderRow(Randomiser.date(), Randomiser.word()))
        val numChildren = Randomiser.int(0, 10)
        for (j in 1..numChildren) {
            if(Randomiser.int(0, 1) &gt; 0) {
                rows.add(ContentAdapter.MessageRow(Randomiser.message()))
            } else {
                rows.add(ContentAdapter.ColourRow(Randomiser.colour()))
            }
        }
    }
    return rows
}</pre>
<h2>Conclusion</h2>
Hopefully after checking out <a href="https://github.com/JakeSteam/StickyHeaders/tree/remove-stickyheaders">the repository</a> or <a href="https://gist.github.com/JakeSteam/97a827d4d7ccced73fed798025f61ea1">the Gist</a> of this tutorial, the approach makes sense. In general, RecyclerViews are extremely powerful, and should almost always be used when there's a dynamic list of content to be displayed, whether finite or infinite.

This approach is easily extendable, and a future post will cover easily adding sticky headers to this RecyclerView implementation. Good luck!