---
layout: post
title: Inside the Data Industry
excerpt: Data industry is moving fast and there is fierce competition in the field. There are thriving businesses in this industry and the reason for this is the expansion of all sorts of IT devices which produce huge amount of data.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: AWS database OLAP OLTP SaaS
image: 1_inside.png
---
<div class="row">
<p>
<img src="/img/posts/1_inside.png" alt="brain" align="right">
Data industry is moving fast and there is fierce competition in the field. There are thriving businesses in this industry and the reason for this is the expansion of all sorts of IT devices which produce a huge amount of data. Once you own huge amount of data you want to process them for a simple reason: Data hides a lot of information such as people behaviour, market segment change, various historic milestones etc. Based on such information you can target your marketing campaign, increase your sales revenue, relocate your resources on a different task, verify some gossips and so on so forth. Who have the information have also technological advantage; they can innovate and have the power to influence others people decisions. It also improves general knowledge how the world works. The market is naturally driven by people's lust for knowledge, for power but also by desire to compete with one another.
</p>

<p>Those who don't have information stay behind and can lose competitive advantage. No wonder there are over $2 billion investment from venture capitalists into data-processing oriented companies, currently the big market players are <a href="http://www.crunchbase.com/organization/cloudera">Cloudera ($1.2 billion)</a>, <a href="http://www.crunchbase.com/organization/mongodb-inc">Mongo DB ($231 mil)</a>, <a href="http://www.crunchbase.com/organization/couchbase">CouchBase ($116 mil.)</a>, <a href="http://www.vertica.com/">Vertica</a> ($30 mil., acquired for <a href="http://www.businessinsider.com/hp-vertica-engineers-leave-for-startups-2013-5">$350 mil.</a> by HP), <a href="http://www.crunchbase.com/organization/paraccel">ParAccel ($84.5 mil.)</a>, <a href="http://www.crunchbase.com/organization/domo">Domo ($248 mil.)</a>, <a href="http://www.crunchbase.com/organization/good-data">GoodData ($101 mil.)</a>. Let's not forget the in-house billion investments in companies like SAP (<a href="www.saphana.com">SAP HANA</a>), <a href="http://www.oracle.com/us/corporate/features/database-in-memory-option/index.html">Oracle</a>, <a href="http://www-01.ibm.com/software/data/netezza/">Netezza</a>, <a href="http://www.teradata.com">Teradata</a>, Google (<a href="https://developers.google.com/bigquery/">Big Query</a>), Facebook (<a href="http://www.presto.io">Presto</a>). This is an ongoing evolution. There will be winners and losers. For technical people interested in data world, I would recommend well-maintained <a href="http://www.dbms2.com">blog</a> which covers a lot of current databases and also important <a href="http://en.wikipedia.org/wiki/Business_intelligence">BI vendors</a>. It has been around for a long time and brings interesting insights.
</p>

<h3><img src="/img/posts/1_database.png" alt="data"> Databases</h3>
<p>
Let's focus just on databases. During the development of our database, I realised how hard it is to orientate in the current world of databases even for software developers. The database term is used on quite the large scale of products including <a href="http://en.wikipedia.org/wiki/Relational_database">relational databases</a>, <a href="http://en.wikipedia.org/wiki/Graph_database">graph databases</a>, <a href="http://en.wikipedia.org/wiki/Document-oriented_database">key-value(document) databases</a> and even indexing and search engines. Non-IT person either don't know what database mean at all or have various perception mostly that a database is a list of items. I will stick with a broad and vague meaning of the database - it is a system that provides access to data.
</p>

<p>
Overall there are <a href="http://db-engines.com/en/ranking">hundreds different databases</a> on the market though many of them are very niche players. The current world of databases is thus also very vibrant and vivid with quite a lot of old and new rivalries (<a href="http://sqlmag.com/database-performance-tuning/microsoft-sends-oracle-cease-and-desist-order">Oracle vs. Microsoft</a>, <a href="http://www.informationweek.com/software/information-management/oracles-ellison-tries-to-outmaneuver-sap-hana/d/d-id/1111638?">Oracle vs SAP</a>). New database appears almost every month. For example few months ago I spoke about our database with a guy from a small US-based company, and, surprisingly he told me their 2 developers was working on key-value database for 2 years and plan to go public soon. On the other hand, we met guys from ScalienDB in 2011 while working in my previous company. In the end they failed after trying three years, interestingly, they published <a href="http://arxiv.org/pdf/1302.3860.pdf">white paper</a> on the design and the reasons why they failed. Also, the analytical database Vectorwise was acquired by Actian company which acquired ParAccel (AWS Redshift is derived from ParAccel). Vectorwise is either discontinued or being merged together with ParAccel. So don't expect to have a complete list to be put anywhere.
<!-- could name some other data companies like Youcalc, LucidEra that either weren't much succesfull or failed. -->
</p>

<p>
Even though, the biggest database player is Oracle, it doesn't have such dominant position like Microsoft in PC desktop world, Google in search industry, Facebook in social networking. Many developers know about Oracle or databases like Microsoft SQL server, Postgres, Mysql. Some of them may know Teradata, DB2, MariaDB. Not many of them know about <a href="http://www.memsql.com/">MemSQL</a>, <a href="http://www.vertica.com/">Vertica</a>, <a href="http://voltdb.com/">VoltDB</a>, <a href="http://www.saphana.com">SAP HANA</a>. Some may have heard about <a href="http://prestodb.io/">Presto</a> (Facebook), <a href="https://developers.google.com/bigquery">Big Query </a>(Google), <a href="http://aws.amazon.com/redshift">Redshift</a> (Amazon). And these are just the most known databases. Then there are schema-less better say key-value systems which are sometimes also considered databases e.g. Riak, Cassandra, Mem-cached, MongoDB, CouchBase, Redis and also Splunk, ElasticSearch for data processing and monitoring.
</p>

<p>
Nearly 10 years ago several companies started developing analytical(see below) columnar databases. Interestingly ParAccel and Vertica started working on their database in 2005 as newly established companies. According to <a href="http://paraccel.us/blog/2010/09/#.VBrwI6a5s1w">John Santaferraro from Actian</a>, he wrote to Michael Stonebraker if he knew any really good database engineers to join their startup. He allegedly responded that he was also doing a startup which was Vertica. Also in 2005 SAP started their work on <a href="http://en.wikipedia.org/wiki/SAP_HANA">SAP HANA</a> by quietly <a href="http://www.scribd.com/doc/237711126/SAP-HANA-Essentials">acquiring companies</a> Callixa and P*time <!-- *p --> and putting distinguished database engineers into the same team - the Tracker team. Last but not least Google's BigQuery is based on Dremel. Dremel has been in production since 2006, so they must have started with the development even earlier. Is this a coincidence or a sort of company espionage :-)?
</p>

<h3><img src="/img/posts/1_sheep.png" alt="data"> Separating sheep from goats</h3>
<p>
Though not very often the problems arise when your database reaches the limit and you try to look for another database. In such case, you really want to be sure that your choice of database will be the right one for at least the next few years. The reason is that your database sits at the bottom of your system. It's technically hard to replace it, takes a lot of effort and company resources which could be spent elsewhere and so the replacement is very expensive. It's usually hard to pick just the one database, however, as the database guru <a href="http://en.wikipedia.org/wiki/Michael_Stonebraker">Michael Stonebraker</a> says <a href="http://searchdatamanagement.techtarget.com/news/2240113325/Michael-Stonebraker-predicts-trouble-for-relational-databases-in-2012">"One size doesn't fit all"</a>. It always boils down to your use cases and you must do a lot of assessments and consider a lot of unknown answers and assumptions. With all options available there is always some sort of tradeoffs.
</p>

There are a few business and technical criteria when choosing an (analytical) database:
<ul>
<li>type of deploy - in-house(on-premise) vs. cloud</li>
<li>scalability - cloud scalability vs. scalable on more nodes (<a href="https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling">up and out</a>) vs. single-node databases</li>
<li>schema vs. schema-less data model (usually key-value)</li>
<li>in-memory vs. disk-based databases</li>
<li>transactional(OLTP) vs. analytical(OLAP)</li>
<li>total cost of ownership - free vs. paid (licensed vs. subscription based models) </li>
<li>set of functional product features
</ul>
</p>

<p>
One of the major databases subdivision is whether the database is suited for <a href="http://en.wikipedia.org/wiki/Online_analytical_processing">OLAP</a> or <a href="http://en.wikipedia.org/wiki/Online_transaction_processing">OLTP</a> systems. As we develop analytical database we are focusing here just on OLAP-suited databases. The main difference between OLAP and OLTP system is the read/write ratio and data volume. In OLAP system, you require asking the system a lot of questions mostly on a large amount of data. You also typically load the data into the system in batches. So the read operation is used much more often than write operation in OLAP system. In OLTP it's the other way round - ie. you need to write or change tiny data very often. Obviously these areas overlap, but usually the database can excel only in one of the category. About twenty, thirty years ago database designers were persuaded, that the same database will be able both to store data quickly and be hit by queries often. However due to processor architecture ten or fifteen years ago it turned out, that it's better to specialize OLAP-suited databases and design them very differently. SAP claims that they can do both OLTP and OLAP well.
</p>

<p>
The technology matured so much over the last 10 years that you can eventually start considering Saas system for analysis or even BI providers like <a href="http://www.gooddata.com">GoodData</a>. Nevertheless, this is a broad topic so I am not going to discuss it here. Just let me point out using <a href="https://aws.amazon.com/marketplace">AWS</a> you can have new running instance with Vertica, SAP HANA and AWS Redshift within 10-40 minutes. Big query is perhaps the only true <a href="http://en.wikipedia.org/wiki/Multitenancy">multitenant</a> database - you don't have to install it and you use the API to access it. They have nearly zero interruptions in service and can update the system whenever they need to. Although it's not the intention of other vendors, it is real technological and process workflow challenge - that's what separate boys from men :-). Google has a giant technological advantage in this area - you have to have a lot of skills, intelligence and know-hows.
</p>

<p>In the above criteria, I haven't mentioned performance. Everybody knows that time is money, however I consider speed as a feature. It is well summarized by <a href="http://en.wikipedia.org/wiki/Larry_Page">Larry Page</a>
<blockquote cite="http://www.bookofspeed.com/chapter1.html"> "As a product manager you should know that speed is product feature number one."</blockquote>
And measuring this feature is always hard. You can go through some benchmark tests which may help you in the evaluation and narrowing your choice down to a few candidates.  Nevertheless, once you narrow down your search I would recommend to do the testing for all candidates in the environment which should be close to the production environment - using real data and real workload. Don't expect, don't predict. Measure and get the numbers. Learn from yesterday, live for today and hope for tomorrow. Have a nice day.
</p>


</div>
