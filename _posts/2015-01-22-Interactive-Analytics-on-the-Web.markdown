---
layout: post
title: Interactive Analytics on the Web
excerpt: Take demographic stats, salary surveys, employee satisfaction, your company sales stats. It all comes down to one people's need - People like to measure.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: database interactive&#32;analytic latency OLTP
image: 2_world.png
---
<p>
Many of our readers know <a href="http://www.google.com/analytics/">Google analytics</a>, which gives statistics about the website traffic, customer conversion and sales. Or Youtube (video) <a href="http://googleblog.blogspot.com/2008/03/insight-into-youtube-videos.html">analytics</a>, Twitter <a href="https://analytics.twitter.com/about">analytics</a>, Facebook <a href="https://developers.facebook.com/docs/platforminsights">Insights</a>, LinkedIn statistics and others. Or have you seen Google <a href="http://www.google.com/publicdata/directory">Public Data Explorer</a> with splendid interactive graphs? Take demographic stats, salary surveys, employee satisfaction, your company sales stats. It all comes down to one people's need: 

<blockquote>People like to measure.</blockquote>
</p>

<p>
Most of these analytics support interactivity and are directed towards a user. They don't work in the 90's reporting style where you export a graph to an image and then send it to your boss. You can work with data and explore them. By encouraging the user to explore his data you increase the chance that the user will discover some interesting facts, that could potentially improve his processes or change his decisions. However, the user experience should be smooth. Because if you don't have the possibility of the interactive work the system hampers the data exploration.
</p>
<p>Sluggish application gives the user bad experience and discourages him from using your application. We live in the age of Google, which in a certain way sets the standards of speed for the application behavior. For how long would you watch a Youtube video if it kept stopping for a while each 5 seconds? You may have tolerated that in 2005, which is 10 years ago.
</p>

<p>
Wouldn't it be nice to have analytic system that allows many users to ask the system interactively and smoothly many queries over (large) set of data? Making such system
with limited resources in a real world is pretty hard. The reason is that a number of concurrent users and data volume go against the interactivity - increasing data volume or number of queries lowers the system throughput and thus the system interactivity. You can increase the throughput by parallelization and other techniques, but all of them have <a href="http://en.wikipedia.org/wiki/Amdahl%27s_law">certain</a> boundaries. Hence, you are restricted in what you can achieve within a given timeframe and set of resources.
</p>

<p>On the other hand, with continuously growing computer performance while keeping or lowering the price, the possibilities increase in our area. Secondly, if you know real-world behavior and data characteristics you can apply specialized algorithms that can boost performance of the system. An obvious question comes to our mind: Where are the boundaries?
</p>

<p>Let's specify the requirements a little bit more. The interactive analytic system should meet these four criteria:
<ul>
	<li>it should be fast (interactive response within 1-2s)</li>
	<li>support many users (serve thousands of users within 1s)</li>
	<li>work on a reasonable amount of data (millions of rows)</li>
	<li>have affordable development and running cost</li>
</ul>

<h2><img src="/img/posts/2_analytic.png"> Specialized system and universal system</h2>

<p>
How can you tackle the problem? One solution is to have a specialized system.
The advantage of a specialized system is the speed. You can tune a specialized
system to be the fastest one. The disadvantage is the development and maintenance
cost. Last but not least, it is the specialized system, so it cannot be well re-used
and commoditized. It is typically very expensive. It is also less flexible,
which means that you are frequently limited to a certain subset of possible reports.
</p>

<p>An universal system is more flexible, but the speed is more sensitive to the performance
of the backend database. You can boost performance by extra investment to hardware
or by using a new kind of database optimized for this type of load.
</p>

<h2><img src="/img/posts/2_latency.png"> Network latency</h2>

<p>
There are many interactive applications that download all necessary data to the client device.
Nevertheless from a certain size of data they have to be stored in a centralized way for two reasons.
The first one is the size of the data itself - which affects the amount of transferred data
and occupied memory of the client device. The second one is the speed of query execution.
The client device usually doesn't have enough computational power.
The centralized server can be better utilized because it allows to reuse
a result of previous queries.
</p>

<p>
<img src="/img/posts/2_world.png" align="right">
Unfortunately, the centralized solution brings network latency on the stage,
which affects the user experience.
A longer distance between the end user and the central server causes higher network latency.
The network latency is influenced by the distance (speed of light in a glass cable
is <a href="http://en.wikipedia.org/wiki/Velocity_factor">around 200,000 km/s</a>,
which results in a <b>1 ms</b> of latency for every <b>100 km</b>),
transmission medium, <a href="http://en.wikipedia.org/wiki/Hop_%28networking%29">
number of hops</a> and the protocol used for communication.
</p>

<p>
Luckily cloud infrastructure gets commoditized as well - at least 4 of the cloud
providers can get the response request to at least billion of people <a href="https://cloudharmony.com/speedtest">within 90 ms</a>.
That allows the end-user application to perform 11 consecutive requests or even more within a second.
It gives a lot of potential for interactive application.
</p>

<h2><img src="/img/posts/1_database.png"> MIA DB - We've had our share of ups and downs</h2>

<p>
As mentioned in the <a href="{{page.previous.url}}">previous post</a>,
there are many databases. During the last 8 years, we have tried many of them.
Sadly, we haven't found the one which would meet our criteria.
So in 2012 we started the research on fundamentally different database architecture.
</p>

<p>
We used our experience from the environment of highly loaded BI Saas system.
After many failed attempts, our fair share of bumpy roads and heavy winds,
we finally came up with the solution - new analytical database designed for low latency
and high throughput. It sounds very familiar for ordinary OLTP database.
Nevertheless, the OLTP database can achieve low latency and high throughput
only for requests related to a small amount of data. But for specific BI
SaaS load we can achieve the same behavior even for much higher amount of data.
</p>

<p>
We call our database Multi-user Interactive Analytical database (abbreviated as MIA DB).
MIA is very suitable for the use in the cloud. In other words, MIA DB is convenient
for workload which is coming from multi-user and <a href="http://en.wikipedia.org/wiki/Multitenancy">multitenant</a>
SaaS applications. In such case, the data are stored in thousands of tables
and the queries are coming in thousands and more - i.e. query/read intensive
relational database. From the technological point of view, MIA is multicore in-memory columnar
database with sophisticated microcaching. The majority is written in
<a href="http://www.erlang.org/">Erlang,</a> but performance critical parts are written in
<a href="http://en.wikipedia.org/wiki/C_%28programming_language%29">C</a>.
</p>


<p>
Ok, there are other problems, e.g. with the presentation layer itself. But you have to start somewhere.
In the <a href="{{page.next.url}}">next blog post</a>,  we will show you a tiny application
with real and publicly available data
build upon our database.
</p>
