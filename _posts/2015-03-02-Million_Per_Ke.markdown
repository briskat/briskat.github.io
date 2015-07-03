---
layout: post
title: 1,000,000 Per Ke?
excerpt: Have you ever asked how many can we do per ke? We perform a simple test to determine a safe usage limit of incoming requests for our MIA database.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: AWS gitcharts ke MIADB performance
image: 4_clock.png
---
<p>The <a href="http://en.wikipedia.org/wiki/Ke_%28unit%29" target="_blank">ke</a> is a traditional Chinese unit of decimal time.
<img src="/img/posts/4_clock.png" class="img-responsive" align="left" width="150" height="98">
It's equal to 1 centiday and lasts 1/100 of one day.
That is 864 seconds which equals to 14 minutes and 24 seconds.
In this blog post, we will perform a simple <a href="http://en.wikipedia.org/wiki/Stress_testing" target="_blank">stress test</a>
to determine a safe usage limit of our <a href="/index.html#TECHNOLOGY" target="_blank">MIA database</a>.
We want to know the maximum number of requests that can be safely served in a second by MIA DB.
We will simulate user requests that come from our <a href="http://gitcharts.briskat.com:8080/dashboard/Rails?days=5" target="_blank">Gitcharts analytic</a>.
This will tell us how MIA DB scales up on modern processors.
Presumably, we also want to find out how many requests can be served per ke.
</p>

<h2>Testing scenario</h2>

<p>In Gitcharts analytics, there is a summary report for the whole repository. And also 8 other reports that show statistics for the last X days.
There is a slider in the application that controls the number of parallel requests sent by a browser, at 4 requests per slider value:

<pre>
/api/p/Project/r/summary,
/api/p/Project/r/winners?days=Days
/api/p/Project/r/last_x_days?days=Days
/api/p/Project/r/files_changed?days=Days

where  Days 1 .. 30
	   Project is one of AngularJS, Bootstrap, Django, Docker, Ember.js, 
	                    GCC, Git, Jekyll, Jenkins, CI, jQuery, Linux,
						MongoDB, PostgreSQL, Puppet, Rails, youtube-dl
</pre>

Performance test simulates random changes of the slider value.
Each test set have between 50 and 400 parallel clients based on the number of processor cores available.
Client selects randomly a project from 16 existing projects.
We run the test set for a few minutes so at least all combinations
of days and projects are examined. After that, we divide a total time by number of all requests.
Application server with MIA DB is deployed on <a href="http://aws.amazon.com/" target="_blank">AWS</a>.
We run each set at least 3 times and made the average from all the attempts.
</p>

<h2>AWS environment</h2>

<p>The following table summarizes some results:
<table class="table table-condensed table-hover">
<thead>
<tr class="info">
<th>Instance type</th><th>Cores</th><th>Avg. Requests/s</th><th>On-demand price/hour</th>
</tr>
</thead>
<tbody>
<tr>
<td>m1.small</td><td>1</td><td>180</td><td>$0.044</td>
</tr>
<tr>
<td>t2.micro</td><td>1</td><td>430</td><td>$0.013</td>
</tr>
<tr>
<td>c3.2xlarge</td><td>8</td><td>1942</td><td>$0.42</td>
</tr>
<tr class="active"><td colspan="6">config: 3.16.0-4-amd64 (Debian), Erlang 17.3, gcc 4.9</td></tr>
</tbody>
</table>
</p>
<p>C3 series on Amazon are instances suggested for these use cases:
"high performance front-end fleets, web-servers, ...". So we picked it up.
M1 series was <b>deprecated</b> on Amazon this month.</li>
Even the t2.micro is <a href="https://aws.amazon.com/blogs/aws/low-cost-burstable-ec2-instances/"
target="_blank">burstable</a> instance it behaved quite well
even in a series of tests. Actually better than older m1.small instance.
As you can see we reached 1942 requests per second on c3.2xlarge instance.
</ul>
</p>

<h3>Gotcha</h3>

<p>During testing on AWS, I didn't realize one gotcha. The bottleneck can be also on your side.
Firstly I tested it from my laptop to Amazon, but the bottleneck was my local network.
I could not generate enough requests from my local network onto Amazon.
</p>

<p>Not only that. There are a lot of parameters to create an OS image. Based on that, throughput
of incoming network may vary a lot. E.g. even the hardware
configuration is very similar request throughput is quite different on AWS, MS Azure, and Google Compute Engine.
</p>

<h2>Local network</h2>

<p>
Though the numbers are quite high, the cloud environment with virtual machines
is a bit unpredictable in a term of a speed. Specially t2.micro instance 
is just for very rough estimation. During the different days
of the week and phases of the day the performance differs. You have to make many measures.
</p>

<h3>T410</h3>
<p>
Let's see how we perform in more isolated environment - on a local network.
First we ran a test on Lenovo T410 laptop. The results are as follows:

<table class="table table-condensed table-hover">
<thead>
<tr class="info"><th></th><th>Options</th><th>Reqs/Sec</th><th>Difference</th></tr>
</thead>
<tbody>
<tr><td>T0</td><td>logs on</td><td>733</td><td></td></tr>
<tr><td>T1</td><td>logs off</td><td>1005</td><td>T0 / T1 = 0.72</td></tr>
<tr class="active"><td colspan="4">Lenovo T410,  i5-540M 2.53 GHz, 3MB Cache, 4 threads, HDD, 3.16 (Debian)</td></tr>
</tbody>
</table>

<p>
Because logging always has some performance overhead so we tried to switch it off completely.
It's interesting to find out how much the overhead of logging is. It was over a quarter of total time.
However on a different laptop on the local network and also on AWS c3.2xlarge it was
less than 10% of a total time. That means if we change the way we do logging
we can gain in speed improvement. We have some thoughts on this.
</p>

<h3>T430s, write optimized</h3>
<p>Secondly we ran the test on a bit better laptop. We also set ext4 options
to data=writeback + nobarrier so we could
increase the disk writing speed (ext4 write-optimized option in the table).
</p>


<table class="table table-condensed table-hover">
<thead>
<tr class="info"><th></th><th>Options</th><th>Reqs/Sec</th><th>Difference</th></tr>
</thead>
<tbody>
<tr><td>T2</td><td>logs on, gc on, ext4 write-optimized</td><td>1242</td><td></td></tr>
<tr><td>T3</td><td>logs on, gc off, ext4 write-optimized</td><td>1658</td><td>T2 / T3 = 0.75</td></tr>
<tr><td>T4</td><td>logs off, gc on</td><td>1665</td><td>T2 / T4 = 0.75</td></tr>
<tr><td>T5</td><td>logs off, gc off</td><td>1820</td><td>T4 / T5 = 0.91</td></tr>
<tr class="active"><td colspan="4">Lenovo T430s, i7-3520M 2.9MHz, 4MB Cache, 4 threads, SSD, 3.16.0-4-amd64 (Debian)</td></tr>
</tbody>
</table>
</p>

<p> The results can be seen in the table above.
Performance overhead also applies to a garbage collection of old caches.
To be able to run garbage collection effectively, additional information has to be stored.
In special cases, it has a sense to completely switch off gathering of this information.
Thus, we measured the performance overhead.
The gap between T2 and T4 is quite high. It can be caused by more context switches
and we don't know yet the reason behind it. However, it's another possible place
for improvement.
</p>

<h2>1,000,000 per ke</h2>
<p>We can see that determining throughput of requests is quite unpredictable.
We also see that MIA DB scales up i.e. that running MIA DB 
can use multiple cores on instance. Maximum throughput of 1200 requests
per second on an ordinary laptop is really a good result.
That's actually over a million requests per ke or over 100 million requests per day
or 3 billion requests per month. 
</p>

<p>
Well, it's not that impressive from the angle of <a href="http://www.computerworlduk.com/news/infrastructure/3433595/boeing-787s-to-create-half-a-terabyte-of-data-per-flight-says-virgin-atlantic/" target="_blank">Internet of Things</a>
and their numbers. You would still need one-third of peta years to generate your
first brontobyte (1 brontobyte = 2^90 bytes &asymp; 10^27 bytes ) of log data :-).
But be let's be more realistic. If you had over
3 billion page views on your website you would be safely in the top 500 most trafficked websites in the world -
<a href="http://www.nbcnews.com/id/31066137/media-kit/" target="_blank">
NBC news</a> is around the <a href="http://www.alexa.com/siteinfo/nbcnews.com" target="_blank"> spot 500</a>
on Alexa rank has 1.2 billion page views per month.
</p>
