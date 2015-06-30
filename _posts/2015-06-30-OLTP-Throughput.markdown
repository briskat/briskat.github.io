---
layout: post
title: Query Throughput on Analytics on OLTP (Postgres)
excerpt: In our fifth blog post, we showed how positive effect automatic caching can have on performance boost. For better comparison let's find out approximate request throughput of our application on PostgreSQL.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: PostgreSQL OLTP OLAP performance
hashtag: OLTP,OLAP
image: 7_caution.png
---
<p>In our third <a href="/blog/Eine-Kleine-Git-Analytik">blog post</a>,
we showed simple Git analytic using our
<a href="http://en.wikipedia.org/wiki/Online_analytical_processing">OLAP</a>
MIA database with automated micro-caching. You can check updated version of
the git analytic application <a href="http://gcharts.briskat.com">here</a>.
In our fifth blog post, we also showed how positive effect automatic
caching can have on performance boost. For better comparison
let's find out approximate request throughput of the application on PostgreSQL.
</p>

<p>PostgreSQL is an <a href="http://en.wikipedia.org/wiki/Online_transaction_processing">OLTP</a>
database but it is used in analytics as well as many other OLTP databases.
In fact, it's quite fast in an analysis of a small amount of data.
On the amount of data that's in git analytic, Postgres performs queries
faster than many of the new OLAP databases.</p>

<p>The reason is that many OLAP vendors don't pay much attention to the query
throughput on a small amount of data. I actually read a comment from one
OLAP vendor: "We are a data warehousing vendor. We take queries that run minutes or hours
and run them in seconds. And we also take queries that
run milliseconds and run them in seconds." That was obviously
a joke, but not that far from the truth.
</p>

<h3>Test</h3>
<p>For our test, we take dashboard with reports and simulate
request that is necessary to compute the reports on the dashboard.
We took 16 projects and run series of selects that correspond to requests
from the application. These reports can be written as the following
select statements:</p>
<a href="http://gcharts.briskat.com">
<img src="/img/posts/7_changes.png" class="img-responsive" alt="code changes"></a>
<pre>-- commits by day
SELECT date_id, COUNT(*)
FROM commit
WHERE date_id>16611
GROUP BY date_id

-- size changed by day
SELECT date_id, SUM(size_changed)
FROM fact, commit
WHERE commit_id=commit.id AND date_id>16611
GROUP BY date_id

-- file changed, added, deleted, modified by date
SELECT date_id, file_change_type_id, COUNT(*)
FROM fact, commit, file_change_type
WHERE commit_id=commit.id AND file_change_type_id=file_change_type.id
GROUP BY date_id, file_change_type_id HAVING date_id>16611

-- lines added and deleted by day
SELECT date_id, SUM(lines_added), SUM(lines_deleted)
FROM fact, commit
WHERE commit_id=commit.id AND date_id>16611
GROUP BY date_id
</pre>

<a href="http://gcharts.briskat.com"><img src="/img/posts/7_authors.png" class="img-responsive" alt="top_achievers"></a>
<pre>-- commits by author since certain date
SELECT author_id, COUNT(*) AS CNT FROM author, commit
WHERE author_id=author.id AND date_id>16611
GROUP BY author_id

-- size changed, lines added and deleted by author since certain date
SELECT author_id, SUM(size_changed), SUM(lines_added), SUM(lines_deleted)
FROM author, commit, fact
WHERE author_id=author.id AND commit.id=commit_id AND date_id>16611
GROUP BY author_id
</pre>

<p>Postgres when running reports on Code and Author tabs for the last 10-40 days
were able to execute queries that correspond to 256 req/s and 384 req/s. See the table below.
</p>

<table class="table">
<thead>
<tr><th colspan="3" class="text-center">Last 10 - 40 days</th></tr>
<tr class="info"><th>Tab</th><th>PostgreSQL</th><th>Briskat (MIA DB)</th>
</tr>
</thead>
<tr><td>Code</td><td>256 reqs/s</td><td>944 reqs/s</td>
</tr>
<tr><td>Author(s)</td><td>384 reqs/s</td><td>1626 reqs/s</td>
</table>

<p>So you can make a few hundred requests per second.
Quite a good result. It is not surprising since
each select is an aggregate of a small amount of data.
That's a thing which PostgreSQL is really good at.
But you can also see that even OLAP database can be
faster even on a small amount of data.
</p>

<table class="table">
<thead>
<tr><th colspan="3" class="text-center">Last 335 - 365 days</th></tr>
<tr class="info"><th>Tab</th><th>PostgreSQL</th><th>Briskat (MIA DB)</th>
</tr>
</thead>
<tr><td>Code</td><td>16 reqs/s</td><td>481 reqs/s</td>
</tr>
<tr><td>Author(s)</td><td>42 reqs/s</td><td>415 reqs/s</td>
</table>

<p>After changing the day range to the last 330-360 days,
the throughput dropped down nearly 16x and 9x. Which is quite a lot.
You could tune up the queries a bit, perhaps
change some configuration setting in the database
( <a href="/files/pg_gitcharts.zip">download files</a> ).
Sometimes it will make a difference but more often it won't.
</p>

<p>This kind of speed drop down is not particularly linked
to PostgreSQL, but to all row-based OLTP databases.
Once you want to make a query which aggregates
result of many rows, the performance will start dropping
dramatically. A certain amount of data and speed of processing
can be a tipping point to try some OLAP databases.
</p>

<p>You can, however, notice, that on our database the throughput
of requests also dropped 2x and 4x. And you should wonder why when there
is a caching.
</p>

<p>The output of user reports in second test case is several times longer.
To save memory, we cache ids (numbers) only in our database.
So every time you ask for the result, numbers have to be translated
back to labels. The slowdown is very approximately 1ms for each 1k
of rows in the user report. We could perhaps get up to 700req/s,
but there is an overhead. Nevertheless the data aggregation is
much more expensive, so it's still worth it.
</p>

<h3>Yesterday's big data are normal data today</h3>
<p>This test illustrates that in almost any situation there are
cases that have a various influence on performance and you
should be aware of them.
</p>
<p>We also wanted to show how important the query throughput is from
the point of serving many users. If your query throughput drops 10
times it means you can process 10 times less concurrent users.
</p>
<p>Lastly, we showed here that even OLAP database can perform pretty well
on a small amount of data. Small data is important as well.
They too contain valuable information. And actually yesterday's
big data are quite often small or normal data today.
</p>
