---
ID: 2093
post_title: >
  Scheduling A Repeating Background Task
  On Android, With Power / Internet
  Requirements
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/scheduling-a-repeating-background-task-on-android-with-power-internet-requirements/
published: true
post_date: 2018-12-03 18:32:17
---
Whilst developing Android apps, performing a scheduled task at set intervals is a very common requirement. Despite this, there are a surprisingly high number of solutions, each with their own advantages and disadvantages. This tutorial will focus on using <a href="https://github.com/firebase/firebase-jobdispatcher-android">Firebase JobDispatcher</a>, a library that uses Google Play services to provide a backwards compatible (API 14+) job scheduling library. This makes it an attractive option for those that need to support older devices, and know their users will have Google Play installed:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/comparison.png"><img class="size-full wp-image-2098 aligncenter" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/comparison.png" alt="" width="844" height="155" /></a>

<!--more-->

This tutorial will walk you through the simple steps needed to implement Firebase JobDispatcher, as well as the customisation options available. A minimal example project of this tutorial's implementation is available <a href="https://github.com/JakeSteam/ScheduledJobs">as a repository</a>, or as a <a href="https://gist.github.com/JakeSteam/4d87c6472914c714214d9511db340b09">Gist</a>. Kotlin is used, but all code is straightforward and can be converted to Java.
<h2>Checking scheduled jobs</h2>
Throughout this tutorial, the ability to check the currently scheduled tasks / jobs is extremely useful. As there is no GUI for this, it must be done by running the following in the "Terminal" tab of Android Studio (replacing <code>uk.co.jakelee.scheduledjobs</code> with your app's package name):
<pre>adb shell dumpsys activity service GcmService --endpoints uk.co.jakelee.scheduledjobs</pre>
This will return a <strong>lot</strong> of information, most of it not useful (e.g. every saved WiFi network!). The first of the important parts is the task count, showing the number of registered jobs:

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/scheduledtasks.png"><img class="aligncenter size-medium wp-image-2099" src="https://blog.jakelee.co.uk/wp-content/uploads/2018/12/scheduledtasks-243x300.png" alt="" width="243" height="300" /></a>

The second is the pending &amp; past executions list. This shows your tasks, their internet / scheduling requirements, and their execution history:
<pre>Pending:
(scheduled) uk.co.jakelee.scheduledjobs/com.firebase.jobdispatcher.GooglePlayReceiver{u=0 tag="uk.co.jakelee.scheduledjobs.job" trigger=window{start=15s,end=30s,earliest=-369s,latest=-354s} requirements=[NET_ANY] attributes=[RECURRING] scheduled=-384s last_r
un=N/A jid=N/A status=PENDING retries=0 client_lib=FIREBASE_JOB_DISPATCHER-1}
Not yet run.

Past executions:
[cost:100%] (finished) [uk.co.jakelee.scheduledjobs/com.firebase.jobdispatcher.GooglePlayReceiver:uk.co.jakelee.scheduledjobs.job,u0]
successes: 60 reschedules: 0 failures: 0 timeouts: 0 invalid_service: 0 total_elapsed_millis: 2842 total_uptime_millis: 2848</pre>
<code>[NET_ANY]</code> shows that any internet connection type is acceptable, <code>[RECURRING]</code> shows that I've set it to repeat, <code>window{start=15s,end=30s ... }</code> shows that I've scheduled it to repeat very frequently.
<h2>Adding Firebase JobDispatcher library</h2>
First, add the <a href="https://github.com/firebase/firebase-jobdispatcher-android">Firebase JobDispatcher library</a> to your app-level <code>build.gradle</code>:
<pre>implementation 'com.firebase:firebase-jobdispatcher:0.8.5'</pre>
Next, add a service for the JobScheduler to your <code>AndroidManifest.xml</code>. The <code>.JobScheduler</code> doesn't exist yet, but you'll be making it in the next step!
<pre>&lt;service
        android:name=".JobScheduler"
        android:exported="false"&gt;
    &lt;intent-filter&gt;
        &lt;action android:name="com.firebase.jobdispatcher.ACTION_EXECUTE" /&gt;
    &lt;/intent-filter&gt;
&lt;/service&gt;</pre>
<h2>Create JobScheduler class</h2>
<code>JobScheduler.kt</code> is the class that handles the actual scheduling. The rest of this tutorial will describe the implementation process, but there is also a<a href="https://gist.github.com/JakeSteam/4d87c6472914c714214d9511db340b09#file-jobscheduler-kt"> Gist of the implementation</a>.

First, create the class, making sure to extend JobDispatcher's <code>JobService</code>:
<pre>class JobScheduler : JobService() {</pre>
Next, create a companion object with a tag for your job, so you can identify it later:
<pre>companion object {
    private const val SIMPLE_JOB_TAG = "uk.co.jakelee.scheduledjobs.job"
}</pre>
When a scheduled task starts, <code>onStartJob</code> is called. This method is overridden to trigger the intended job. We'll cover setting the tag later, but for now just check it has been correctly set and pass the <code>JobParameters</code> object to <code>simpleJob</code>. Note that this method returns a boolean determining whether it has more work to do. This should almost always be false.
<pre>override fun onStartJob(job: JobParameters): Boolean {
    Log.d("JobScheduler", "Job started")
    when (job.tag) {
        SIMPLE_JOB_TAG -&gt; simpleJob(job)
        else -&gt; return false
    }
    return true
}</pre>
Next up is actually creating the job function. This can do anything you want (that doesn't require an activity), in this example it just writes a line to the log and finishes the job. Note that <code>jobFinished</code> needs to be passed the <code>JobParameters</code> object initially passed to <code>onStartJob</code>, as well as a boolean for whether the task needs to be retried (usually false).
<pre>private fun simpleJob(job: JobParameters) {
    Log.d("JobScheduler", "Ran job ${job.tag}")
    jobFinished(job, false)
}</pre>
Finally, <code>onStopJob</code> must be overridden. Just like <code>onStartJob</code>, this also has to return a boolean, this time determining whether to retry the job (usually true). <code>onStopJob</code> is called when your job stops during execution, usually because it is taking too long to complete. The <a href="https://stackoverflow.com/a/48630779/608312">normal maximum execution time is 10 minutes</a>, but this can vary, so try to complete your task much quicker than the limit.
<pre>override fun onStopJob(job: JobParameters?) = true</pre>
<h2>Scheduling a job</h2>
Scheduling a recurring job is luckily very simple, and has a lot of customisable options:
<pre>fun scheduleJob(context: Context) {
    val dispatcher = FirebaseJobDispatcher(GooglePlayDriver(context))
    val exampleJob = dispatcher.newJobBuilder()
        .setService(JobScheduler::class.java)
        .setTag(SIMPLE_JOB_TAG)
        .setRecurring(true)
        .setLifetime(Lifetime.FOREVER)
        .setReplaceCurrent(true)
        .setRetryStrategy(RetryStrategy.DEFAULT_EXPONENTIAL)
        .setConstraints(Constraint.ON_UNMETERED_NETWORK, Constraint.DEVICE_CHARGING)
        .setTrigger(Trigger.executionWindow(5, 10))
    dispatcher.mustSchedule(exampleJob.build())
    Log.d("JobScheduler", "Scheduled job")
}</pre>
In this example, the following parameters apply:
<ul>
 	<li><code>setService</code> determines the class called when this job triggers. This is sometimes another class, but it's often easier to trigger the same class as the scheduling occurs in.</li>
 	<li><code>setTag</code> sets the job's tag, used when the job starts to check which job it is.</li>
 	<li><code>setRecurring</code> sets whether a job should repeat or not.</li>
 	<li><code>setLifetime</code> determines if the job scheduling should last <code>UNTIL_NEXT_BOOT</code> or <code>FOREVER</code>.</li>
 	<li><code>setReplaceCurrent</code> decides if this job should replace existing tasks with the same tag, or leave them.</li>
 	<li><code>setRetryStrategy</code> lets you set custom retry strategies if a job fails, or use the built-in <code>EXPONENTIAL</code> / <code>LINEAR</code>.</li>
 	<li><code>setConstraints</code> allows you to set requirements for the job to run. This is used to only allow the task to run on an unmetered network (e.g. WiFi) whilst charging.</li>
 	<li><code>setTrigger</code> lets you either execute a job now (<code>JobTrigger.NOW</code>) or schedule the task. The 2 numbers are the earliest and latest times (since last execution or job scheduling) that the job should be executed. In this example, the task should be run every 5-10 seconds.</li>
</ul>
<h2>Cancelling a job</h2>
Once you have a JobDispatcher instance with <code>FirebaseJobDispatcher(GooglePlayDriver(context))</code>, you can either unschedule all jobs (<code>.cancelAll()</code>), or unschedule individually (<code>.cancel(tag)</code>).
<h2>Conclusion</h2>
Firebase's JobDispatcher library provides a simple way to schedule future tasks. I recently used this approach to implement an app "heartbeat", where it sends a small message to a server every 24 hours. The time of the heartbeat was very flexible, so a large window of activation is provided (20-28 hours). Having this window (in <code>setTrigger()</code>) as large as possible helps reduce your app's battery usage, as it allows the device to stay in <a href="https://developer.android.com/training/monitoring-device-state/doze-standby">Doze mode</a> for as long as possible.

The only downside is the requirement on Google Play Services to centrally coordinate your job scheduling. If you distribute your app via the Play Store this isn't an issue, but will cause serious problems for those distributing externally.

Note that this is just one of the many services Firebase offers, and there is <a href="https://blog.jakelee.co.uk/firebase/">an ongoing service covering each one</a>.