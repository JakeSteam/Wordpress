---
ID: 2946
post_title: >
  Creating the PR template that Future You
  wishes Past You used
author: Jake Lee
post_excerpt: ""
layout: post
permalink: >
  https://blog.jakelee.co.uk/creating-a-pr-template/
published: true
post_date: 2020-12-01 16:14:28
---
How many times have you found a confusing line of code, figured out which ticket it solved, opened the relevant PR and... nothing. There's a few approvals and it's merged, with no explanation / discussion of what on earth is going on!

When this happens, the only way to try and understand the code is to hope the author still works at the company (and remembers ancient, confusing code!). All it takes is a poor memory / high turnover, and there can easily be no helpful information anywhere.

This post will introduce a PR template I've used / refined through multiple teams, and have found extremely helpful. An example PR created using it is <a href="https://github.com/JakeSteam/pr-process-demo/pull/1" target="_blank" rel="noopener noreferrer">available in a sample repo</a>.

PS: I'm very sorry for the ridiculous title, I couldn't resist. <!--more-->
<h2>Why use a pull request template?</h2>
<ol>
 	<li>As mentioned in the intro, it ensures all PRs have enough information to be useful for future developers.</li>
 	<li>A consistent format helps newer developers know what a PR entails, without having to ask.</li>
 	<li>It can remind a developer of a forgotten task, for example when they try to answer "What tests have been added?" and realise none have!</li>
 	<li>Finally, having a structure with distinct sections avoids any "walls of text" or unstructured PRs.</li>
</ol>
<h2>How do I setup a pull request template?</h2>
The actual location varies by code host, but the approach is always a markdown template within a specific folder:
<ul>
 	<li><a href="https://docs.github.com/en/free-pro-team@latest/github/building-a-strong-community/creating-a-pull-request-template-for-your-repository" target="_blank" rel="noopener noreferrer">GitHub</a> (also has limited template support)</li>
 	<li><a href="https://docs.microsoft.com/en-us/azure/devops/repos/git/pull-request-templates?view=azure-devops" target="_blank" rel="noopener noreferrer">Azure DevOps</a> (also supports per-branch templates!)</li>
 	<li><a href="https://docs.gitlab.com/ee/user/project/description_templates.html#creating-merge-request-templates" target="_blank" rel="noopener noreferrer">GitLab</a></li>
 	<li><a href="https://bitbucket.org/blog/save-time-with-default-pull-request-descriptions" target="_blank" rel="noopener noreferrer">BitBucket</a></li>
</ul>
<h2>The PR template</h2>
The PR template is provided in <a href="https://github.com/JakeSteam/pr-process-demo" target="_blank" rel="noopener noreferrer">an example repository</a>, <a href="https://raw.githubusercontent.com/JakeSteam/pr-process-demo/main/.github/pull_request_template.md" target="_blank" rel="noopener noreferrer">here is the raw markdown</a>, and <a href="https://github.com/JakeSteam/pr-process-demo/pull/1" target="_blank" rel="noopener noreferrer">here is an example PR raised with it</a>.

<a href="https://blog.jakelee.co.uk/wp-content/uploads/2020/12/kVXcYMQ.png"><img class="aligncenter wp-image-2952 size-full" src="https://blog.jakelee.co.uk/wp-content/uploads/2020/12/kVXcYMQ.png" alt="Example PR screenshot" width="939" height="849" /></a>

Here's why each section is essential:
<h4>"Tracked by..."</h4>
Having a link to the actual ticket makes it easy for developers to understand the purpose of the ticket, as well as ensuring information can easily be found.

Make sure to change the URL to match your JIRA / other ticket system format.
<h4>"This PR..."</h4>
Having a simple, one sentence summary of the ticket ensures the PR has a clear overall focus. For example, <em>"This PR..</em>.
<ul>
 	<li><em>adds an email validator to the login screen"</em></li>
 	<li><em>fixes a bug caused by an invalid string reference"</em></li>
 	<li><em>disables the no longer supported Fabric integration"</em></li>
</ul>
<h4>"Considerations and implementations"</h4>
This is the main body of the PR, and varies massively by PR. A simple PR may have a single sentence here, others may have multiple paragraphs, diagrams, etc.
<h4>"How to test"</h4>
Having an ordered, straightforward list of steps to test the changes massively helps overall understanding. This list can also be used as hints for QA, and is a good place to upload any custom configuration files etc needed.
<h4>"Test(s) added"</h4>
Having this section helps reminds developers to always add tests, or explain themselves if they can't!
<h4>"Screenshots"</h4>
Having this section makes visual changes immediately obvious, and means visual bugs can often be identified instantly. Also, the screenshots are useful for non-devs preparing content for sprint review, or signing off work.

It's worth mentioning that in less visual projects, this section could be called "Changes" and contain logs / process flows / etc. This section sometimes contains screenshots of Charles network requests or LogCat messages as well as normal screenshots.
<h2>Conclusion</h2>
Adding a PR template is a very easy change, yet drastically improves documentation quality. It's the dream, writing self-organising documentation as you go! A template also ensures the repository contains its own documentation, instead of relying on employees knowing past tickets.

Since the template provided will probably be customised over time according to how your team functions, <a href="https://github.com/stevemao/github-issue-templates" target="_blank" rel="noopener noreferrer">a repo of example templates</a> may be useful.

Whilst having testing steps on the PR is useful for code review, ideally it should be accessible to QAs &amp; POs too. A basic way to do this is just put a link to the PR on the ticket when moving it to "Ready to test", but I'm sure there are automated solutions!

Finally, remember that if you're using GitHub for PRs you also have <a href="https://blog.jakelee.co.uk/exploring-pull-requests-with-githubs-rich-diff-functionality/">access to the extremely useful rich diffs</a> in PRs!