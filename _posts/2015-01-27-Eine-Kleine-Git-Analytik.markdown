---
layout: post
title: Eine Kleine Git Analytik
excerpt: It's time to make some real example. We will show you tiny application with real data. Software repositories on GitHub are a good choice for our use case.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: AWS database github git gitcharts MIADB
image: 3_gitcharts_sm.png
---
<p>
In the <a href="{{page.previous.url}}">previous post</a>, we have outlined one possible approach on how
to build interactive analytics for a huge amount of users around the globe.
It's time to make some real example. We will show you a tiny application with real data.
</p>

<h2><a href="http://www.github.com"><img src="/img/posts/3_octocat.png"/></a>GitHub</h2>
<p>
For our application, we chose <a href="http://www.wired.com/2013/03/github/">GitHub</a>.
GitHub is one of the best places to host a software project. It's fast, efficient and available to anybody.
There are over 17 millions of both commercial and 
<a href="https://github.com/search?l=&q=followers%3A%3E-1&ref=advsearch&type=Repositories">public repositories</a> with over
<a href="https://github.com/search?l=&o=desc&q=followers%3A%3E-1&ref=advsearch&s=joined&type=Users">2.5 million</a> users, most of them are software developers.
The whole site is managed by 240 employees. Now I can tell you an interesting fact.
Every reader of this blog post knows a company that uses software which can be found on GitHub. 
That is a trivial fact because our web pages use <a href="https://github.com/twbs/bootstrap">Bootstrap</a> and <a href="https://github.com/jekyll/jekyll">Jekyll</a>.
And these web pages are hosted on GitHub as a public repository.
Enough of silly jokes and let's get back to analytics. 
</p>

<h3>The most watched software projects</h3>
<p>Software repositories on GitHub are a good choice for our use case. The users are spread around the globe.
One of the biggest repositories is the Linux repository.
In total it has roughly 15000 authors, 0.5 million commits, 50000 files and 0.5GB in total size
(though the Linux was started in 1991, the Linux git history
<a href="https://github.com/torvalds/linux/commit/1da177e4c3f">only starts from 2005</a>
so it could be quite bigger). If you want to be able to count statistics after each
commit and each file, your fact table, in this case, will have just 1.2 million records.
All other repositories in the top ten
<a href="https://github.com/search?q=stars:%3E1&s=stars&type=Repositories">most watched</a>
GitHub repositories have less than 50000 commits.</p>

<p>So the data are not that big but satisfactory for our purposes.
This example should also show that there are a lot of interesting
"small" data around that are just not used as well as they could be.
And one of the reasons why they are not utilized better is the technological
and cost barrier. Surely, other examples come to my mind.
</p>

<h2>Gitcharts - basic git analytics</h2>

<p>We have picked among the most watched projects and some other eg. like PostgreSQL.
The UI of Gitcharts was developed in <a href="http://emberjs.com">Ember.js</a>, 
<a href="http://getbootstrap.com">Bootstrap</a>,
and <a href="http://morrisjs.github.io/morris.js/">morris.js</a> charting library.
The application starts with the summary report: <center><img src="/img/posts/3_summary.png"/></center>
</p>

<p>
We track just the main branch of the project's repository. 
We don't differentiate between regular and merge commits.
Number of files and repository size are just approximate numbers.
They are counted from regular commits only.
I.e. if your branch includes a lot of merge commits it will differ.
Especially if you merge from remote branch it can differ up to 50%.
But most of the listed repositories have less than 10% error - it's still better to show
an approximate number than not to show anything. To show exact numbers,
we would need non-trivial post-processing in ETL.
</p>

<p>All other reports show statistics for the last X days:
<center><img src="/img/posts/3_slider.png"/></center>
Firstly, we have top achievers reports
<center><img src="/img/posts/3_top_achievers.png"/></center>

and detailed views follow. We show the number of commits, size changed,
files changed, added, removed, and also lines added and deleted for each day
in the last X days. At the top in the graphs, there are totals for the whole period. Eg. in total,
there were 359 commits for the last 22 days in the Ruby on Rails project.
<center><img src="/img/posts/3_reports.png"/></center>
You can surely think about many other reports but for demonstration purposes
we put just a few of them.
<center>
<a class="btn btn-success btn-lg" href="http://gitcharts.briskat.com" role="button">Try Gitcharts</a>
</center>
</p>

<h2>Latency</h2>


<p>The backend server itself is located in Ireland. We did not replicate it to other locations
although if there is an interest we can do it. But for now if you are in most of the Europe
or on the US East Coast you should barely notice the network latency. However, from more
distant locations the network delay may be much more noticeable.
Similarly with the UI, in this case you can actually reckon some time lag.
More low-level charting library would be required to reduce the small
lags.
</p>

<h2>Resource efficiency</h2>

<p>
We run gitcharts application on m1.small instance. It has 1.6 GB RAM with 1 AWS compute unit,
which has actually more than two times smaller computational power than an average nowadays laptop.
You can put there quite comfortably 20 interesting repositories.
</p>


<p>There are no data cached on the client side neither they are on the web server.
With each change of the slider, 4 parallel requests are triggered.
The database executes the reports or hit automatically created micro-caches
and returns the response. We created simple stress test which you
can see in the following video.
	<center>
	<div class="embed-responsive embed-responsive-16by9">
	  <iframe class="embed-responsive-item" src="http://www.youtube.com/embed/xQZSdggFrvU?showinfo=0&rel=0&autohide=1" allowfullscreen></iframe>
	</div>
	</center>
</p>

<p>
As you could see, in the video very nice feature is that the UI client can
make synchronous requests only. Sure, sometimes the client has to repeat
the request, but with this feature we were able to make the stress test
by using simple wget utility.
</p>

<h2>High throughput</h2>

<p>
We have also shown in the video that even on a small computer you can serve decent
amount of concurrent users. In this case, you can serve ~50 different users
or 50 changes of the slider on 16 different projects within one second.
That's ~4 million changes of the slider a day.
</p>

<p>
Ok, if you add more complicated reports and refresh the data more often - it will be less.
Nevertheless not that much, I cannot tell the precise number - as the factors influencing
the speed are so complex that each scenario needs its own measurement. 
We are quite confident regarding the performance because of the architecture and the experience.
</p>

<p>
We have seen the differences in the number of requests served during the day.
It is because the hosted machine on AWS is not a fully dedicated server
but runs as a virtual machine. So it gets affected a little.
During different days and different phase of the day
we have seen the numbers ranging from 43 to 53 users served within a second.
</p>

<p>
We have demonstrated that with only small effort you can build
interactive analytical application and effectively utilize
computer resources. All of that is achieved without
any tuning of any query. Simpler application design without
specialized tuning allows to cut the price significantly.
</p>
