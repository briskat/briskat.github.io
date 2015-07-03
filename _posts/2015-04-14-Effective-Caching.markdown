---
layout: post
title: Effective Micro-caching in Relational Database
excerpt: The caching is integral part of MIA DB design. The reason for it is
    a huge improvement in the speed of requests processing and request
    throughput. At the same time, it simplifies a system design. So you
    basically have all of it or none of it.
author: <a href="http://cz.linkedin.com/in/hynekvychodil">Hynek Vychodil</a>
tags: MIADB performance caching
thumbups: 100
image: 5_engine.png
---
<p>
<img src="/img/posts/5_engine.png" class="img-responsive" align="left"
width="120" height="120" style="padding:5px">
In the <a href="{{page.previous.url}}">previous post</a>, we showed how fast
<a href="/index.html#TECHNOLOGY" target="_blank">MIA DB</a> can be for a considerably
small variety of requests under heavy load. In this case, it is because of caching.
MIA DB quickly computes a cache for all the requests and serves the
rest of them from the cache. It seems unfair, but it's
not. The caching is integral part of MIA DB design. The reason for it is a
huge improvement in the speed of requests and their throughput. At the same time,
it simplifies a system design. So you basically have all of it or none of it.
</p>

<p>
Formerly, in GoodData we verified that caching can improve the speed of a heavily
loaded business intelligence (BI) system. The system in GoodData was based on
caches on application level. The main reason is a lack of caching
capabilities integrated into existing databases. The application level
caching is complicated for implementation and maintenance.
</p>

<p>
It has also its limits. The cache can only be made for a whole query. The
cache identification can be based only on query definition which limits you
to information within tables and columns. It affects caching efficiency
because it prevents cache reusability between queries. Cache creation usually
implies tables creation and it usually doesn't belong to fast operations in
common databases.
</p>

<p>
In MIA DB, we took things a step further and addressed most of those issues. The
caches work on a lower level than the usual query which improves the cache hit ratio.
The cache identification is based on exact data content. It nullifies
requirements on the application which uses the database. The cached
operations are carefully chosen to maximise their reusability.
We call this feature automatic micro-caching.
This feature enforces a different query
execution model which works really well with the typical BI system load.
</p>

<p>
Let's demonstrate automatic micro-caching in a simple example. Our friend
Petr Šimeček examined speed of basic query aggregation on several different databases in
<a href="http://padak.keboola.com/agregace-v-mongodb-oracle-redshift-bigquery-voltdb-vertica-elasticsearch-a-gooddata" title="Agregace v MongoDB, Oracle, Redshift, BigQuery, VoltDB, Vertica, Elasticsearch, GoodData, Postgres a MySQL">
one of his blog</a> posts (in Czech). We link here an original result table:

<!--<img src="https://phaven-prod.s3.amazonaws.com/files/image_part/asset/1181792/Tk3QBats8b0DD9f28eij06Qoaho/medium_resume3.png" style="padding:10px"/> -->
</p>
<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: solid">
        <th>Database</th><th>Query time</th>
    </tr>
</thead>
<tbody>
    <tr> <td>MongoDB</td><td>129s</td> </tr>
    <tr> <td>Oracle</td><td>32s</td> </tr>
    <tr> <td>Redshift dw1.xlarge (50M rows)</td><td>15s</td> </tr>
    <tr> <td>Redshift dw2.large (50M rows)</td><td>7s</td> </tr>
    <tr> <td>Redshift dw1.xlarge (500M rows)</td><td>182s</td> </tr>
    <tr> <td>Redshift dw2.large (500M rows)</td><td>53s</td> </tr>
    <tr> <td>Google BigQuery</td><td>7s</td> </tr>
    <tr> <td>VoltDB</td><td>0.07s</td> </tr>
    <tr> <td>HP Vertica</td><td>2.06s</td> </tr>
    <tr> <td>Elasticsearch</td><td>16.3s</td> </tr>
    <tr> <td>PostgreSQL 9.3.4</td><td>33.5s</td> </tr>
    <tr style="border-bottom: solid"> <td>MySQL 5.5.33</td><td>46s</td> </tr>
</tbody>
</table>
<!--
<tbody>
    <tr> <td>MongoDB</td><td>NA</td><td>129s</td> </tr>
    <tr> <td>Oracle</td><td>NA</td><td>32s</td> </tr>
    <tr> <td>Redshift dw1.xlarge (50M rows)</td><td>151s</td><td>15s</td> </tr>
    <tr> <td>Redshift dw2.large (50M rows)</td><td>NA</td><td>7s</td> </tr>
    <tr> <td>Redshift dw1.xlarge (500M rows)</td><td>&gt;8min</td><td>182s</td> </tr>
    <tr> <td>Redshift dw2.large (500M rows)</td><td>NA</td><td>53s</td> </tr>
    <tr> <td>Google BigQuery</td><td>25min</td><td>7s</td> </tr>
    <tr> <td>VoltDB</td><td>29min</td><td>0.07s</td> </tr>
    <tr> <td>HP Vertica</td><td>121s</td><td>2.06s</td> </tr>
    <tr> <td>Elasticsearch</td><td>161min</td><td>16.3s</td> </tr>
    <tr> <td>PostgreSQL 9.3.4</td><td>2min</td><td>33.5s</td> </tr>
    <tr style="border-bottom: solid"> <td>MySQL 5.5.33</td><td>29min</td><td>46s</td> </tr>
</tbody>
-->
<p>
VoltDB result (Test A, 0.07s) is suspicious compared to other databases.
There is most probably prepared aggregation result during data load.
Even if there are various HW configurations it allows us to put results into
context and compare the speed at least in order of magnitude.
</p>

<p>The dataset originates from this <a
href="http://www.javacodegeeks.com/2013/12/mongodb-lightning-fast-aggregation-challenged-with-oracle.html"
title="MongoDB “Lightning Fast Aggregation” Challenged with Oracle">blog
post</a>. Source data contains 50 millions of rows with two columns, time and value.
Both values are random which is not a typical situation for real data.
Using random data disables compression and some advanced algorithms
to be used for computation in our database. The data looks like the one in the following example.
</p>

<pre>
created_on,value
2012-05-02T06:08:47Z,0.9270193106494844
2012-09-06T22:40:25Z,0.005334891844540834
2012-06-15T05:58:22Z,0.05611344985663891
2012-01-05T20:47:19Z,0.2171613881364465
2012-02-10T00:35:17Z,0.4581454689614475
2012-06-19T17:46:41Z,0.9841521594207734
2012-08-20T21:42:19Z,0.3296361684333533
2012-02-24T20:29:17Z,0.9760254160501063
</pre>

<p>
The databases were tested with a simple query which computes four aggregated
values (metrics) for each day like following SQL.
</p>

<pre>
SELECT EXTRACT(year FROM created_on), EXTRACT(day FROM created_on),
       COUNT(*), MIN(value), MAX(value), AVG(value)
FROM RandomData_T
GROUP BY EXTRACT(year FROM created_on), EXTRACT(day FROM created_on)
ORDER BY EXTRACT(year FROM created_on), EXTRACT(day FROM created_on);
</pre>

<p>
First we ran the query in PostgreSQL, Vertica and MIA DB on various HW to
find out vertical scalability.
</p>

<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: solid">
        <th>Pg</th><th>MIA</th><th>MIA</th><th>MIA</th><th>Vertica</th>
    </tr>
    <tr style="border-bottom: thin solid">
        <td colspan="2">T430s 4c</td><td>AWS 8c</td><td colspan="2">AWS 16c</td>
    </tr>
</thead>
<tbody>
    <tr class="active" style="border-bottom: solid">
         <td>33.5s</td>
         <td>3.5s</td>
         <td>1.91s</td>
         <td>0.88s</td>
         <td>2.06s</td>
    </tr>
</tbody>
</table>

<p>
In the table above we can see nice vertical scaling and in this case we are
faster than those databases.
It is nice, but there is not much visible caching involved
yet. So far, it would just be a little bit faster of a database.
</p>

<p>But what would happen if we loaded an additional million rows? The same query takes only
38ms! It's 4% of the original time. In other databases, it usually takes an even
longer time than in the first example. So now things start to become interesting. We gain
almost two orders of magnitude in speed.</p>

<p>Another nice feature is that
it works right out of the box. We don't need to give MIA DB a hint that the
data is loaded incrementally. You can even do a full data load to a new table or
even in a new customer project and it just works. The reason is MIA DB operates
on a raw data level and automatically detects the same data and the
same operation in them.
</p>

<p>Let's explore this feature in more details.
The original query computes four aggregations at once. In real BI systems
there are usually many queries which come in an unpredictable order and time. For
example, there are four different reports at the same dashboard
which computes different metrics. In this case, we will compute four queries.
The first one will compute SUM, the second one MIN and MAX and so on.
Each row in the following table is separate query execution.
It's run sequentially, but it can run in any particular order.
</p>

<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: solid; border-bottom: thin solid">
        <td>SELECT</td>
        <th>No-caching DB</th>
        <th>MIA</th>
        <th>Speed up</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>SUM</td>
        <td>580ms</td>
        <td>811ms</td>
        <td>0.72</td>
    </tr>
    <tr>
        <td>MIN, MAX</td>
        <td>766ms</td>
        <td>82ms</td>
        <td>9.3</td>
    </tr>
    <tr>
        <td>COUNT</td>
        <td>355ms</td>
        <td>20ms</td>
        <td>18</td>
    </tr>
    <tr>
        <td>AVG</td>
        <td>401ms</td>
        <td>0.4ms</td>
        <td>1000</td>
    </tr>
    <tr class="success" style="border-top: thin solid; border-bottom: solid">
        <td style="font-size: 120%; padding: 0">&Sigma;</td>
        <td>2.102s</td>
        <td>913ms</td>
        <td>2.3</td>
    </tr>
</tbody>
</table>

<p>
As we can see, MIA DB can be a little bit slower for first query
execution but it
pays off once you execute next queries. First query execution comes with cache overhead.
But an interesting and an unusual
thing is that MIA DB don't cache whole queries but only partial operations
which are needed for computation.
</p>

<p>In case of the first query, the caches are
created. They speed up every subsequent aggregation on the same table, but it
doesn't just affect the same query. It speeds up all subsequent queries which
use this table and use different filters.
There are usually many facts in the same table in analytical
projects so collective speed up grows even more.</p>

<p>The first query can be relatively slow. But following requests are
much faster so user experience improves. Psychologically it has an even bigger impact on
perceived system performance. Good user experience shapes the way a user
interacts with the system. It encourages people to explore and gain knowledge.
We can also eliminate the first query problem by computing commonly known
reports shortly after a load of new data.
</p>

<p>
We showed the caching works also for incrementally loaded data. Let us show
how it will work with the previous four requests in this scenario.
</p>

<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: solid; border-bottom: thin solid">
        <td>SELECT</td>
        <th>No-caching DB</th>
        <th>MIA</th>
        <th>Speed up</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>SUM</td>
        <td>592ms</td>
        <td>35ms</td>
        <td>17</td>
    </tr>
    <tr>
        <td>MIN, MAX</td>
        <td>781ms</td>
        <td>8ms</td>
        <td>98</td>
    </tr>
    <tr>
        <td>COUNT</td>
        <td>362ms</td>
        <td>3ms</td>
        <td>120</td>
    </tr>
    <tr>
        <td>AVG</td>
        <td>409ms</td>
        <td>0.4ms</td>
        <td>1000</td>
    </tr>
    <tr class="success" style="border-top: thin solid; border-bottom: solid">
        <td style="font-size: 120%; padding: 0">&Sigma;</td>
        <td>2.144s</td>
        <td>46ms</td>
        <td>47</td>
    </tr>
</tbody>
</table>

<p>
The caching works well in this scenario but has an even bigger effect. The
responses in this time range not only affects the immediate user experience.
The caching also allows to serve many more concurrent users. There is a big probability
that the user will utilise some existing cache even the one that doesn't
request exactly the same report.
The rest of it is computed fast enough for interactive work.
</p>

<p>The speed up is not for free. We traded-off the memory for the gain of
the speed which is a well-known technique called caching. The following table shows
memory overhead of caching.
</p>

<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: solid; border-bottom: thin solid">
        <td>SELECT</td>
        <th>Time</th>
        <th>Memory</th>
    </tr>
</thead>
<tbody>
    <tr>
        <td>load</td>
        <td>18s</td>
        <td>858MB</td>
    </tr>
    <tr>
        <td>SUM</td>
        <td>811ms</td>
        <td>+191MB</td>
    </tr>
    <tr>
        <td>MIN, MAX</td>
        <td>82ms</td>
        <td>+240kB</td>
    </tr>
    <tr>
        <td>COUNT</td>
        <td>20ms</td>
        <td>+37kB</td>
    </tr>
    <tr style="border-top: thin solid; border-bottom: solid">
        <td>AVG</td>
        <td>0.4ms</td>
        <td>+3kB</td>
    </tr>
</tbody>
</table>

<p>
Keep in mind the test data is random which makes it hard to compress. In a
real project, the situation will usually be several times better.
</p>

<p>Let us reiterate that the caching is fully automatic. There are some options
on how to speed up the system - which in a particular case can work very well.
Eg. precomputing results at the time of load if
you know the queries, optimizing particular queries (eg. in Postgres)
or adding projections (e.g. in Vertica). But all of these techniques
require manual work. Once you need to speed up hundreds of different analytic projects
with thousands of unknown queries it doesn't scale
well in terms of human resources because human time is costly.
</p>

<p>
We tried to demonstrate basic concepts of MIA DB on this simple example. The
MIA DB has a unique design and it's behaviour is different compared to common
databases. Actually urban dictionary nicely sums up what MIA DB is:
</p>

<blockquote>
She is a crazy girl. Makes jokes most people do not understand. She is
veryyyy pretty and has nice eyes. You will be stunned when you meet a Mia.
</blockquote>
