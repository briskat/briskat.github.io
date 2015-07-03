---
layout: post
title: Interactive Charts with Google Charts
excerpt: Google is known for its obsession by a speed and they have deep know-how in scalability. Google Charts are used by Google itself and it's battle-proven technology.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: google&#32;charts interactive&#32;analytic performance
thumbups: 37
image: 6_dashboard_sm.png
---
<p>Do you agree live charts are more fun?
We are going to show you some interactive charts here.
In our third <a href="/blog/Eine-Kleine-Git-Analytik">blog post</a>, we created
simple <a href="http://gitcharts.briskat.com/dashboard/rails%20(rails)?days=5">
interactive analytic</a>. Today we build upon this example and just change the UI library, so we will be using:
<ul>
<li>UI: jQuery, Google Charts</li>
<li>Backend: <a href="/index.html#TECHNOLOGY">MIA DB</a>, instant-responses analytic database with automatic micro-caching</li>
<li>Data: Well known projects on GitHub, data are loaded to MIA DB from GitHub every hour</li>
</ul>
</p>

<p>Google is known for its obsession by a speed and they have deep know-how in scalability.
So from many <a href="http://socialcompare.com/en/comparison/javascript-graphs-and-charts-libraries">charting libraries</a>
that exist I decided to use their library afterall it is used by Google itself and you get battle-proven
technology. We will focus here just a bit on the user experience of the library. First impressions of the library are:
<ul>
<li>well documented, <a href="https://google-developers.appspot.com/chart/interactive/docs/gallery">many examples</a>, easy to start with</li>
<li>highly configurable, many out of the box functions</li>
<li>scales well in terms of many data points</li>
<li>free for usage, but requires the Internet connection, the source code is not available</li>
</ul>
</p>

<p>
The API is well documented and you can start playing with the library just
by copying &amp; pasting the examples into an HTML file.
For each graph, there is a plenty of options and you will
see a few that may be handy.
</p>

<h3>Pie chart</h3>
<p>Following <a href="https://google-developers.appspot.com/chart/interactive/docs/gallery/piechart">pie chart</a> shows
proportional number of commits by a particular author
for specified amount of days.
</p>

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="http://gcharts.briskat.com/assets/pie.html"></iframe>
</div>

<p>When you change the project name or the day number data for the chart(s)
are pulled off into the graph over xhttp requests
directly from a database every time (you can press Ctrl+Shift+I in FF, Chrome to check network
traffic). Every time whole graph is redrawn from the incoming data.
</p>

<p>On an ordinary laptop drawing up to a thousand values in a pie chart is a piece of cake
for Google charts. There are no noticeable delays (eg. choose Linux repository
from a drop down menu).
When there are too many small (non-significant) areas they are automatically squashed
into 'other' section. That's quite nice out of the box feature.
When a graph legend is too long it's paged automatically.
</p>

<h3>Scatter chart</h3>
<p>In a scatter chart and a bubble chart, you will notice some slight delay,
but still it's quite fast even for a decent amount of data points.
</p>

<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="http://gcharts.briskat.com/assets/scatter.html"></iframe>
</div>

<h3>Bubble chart</h3>
<p>In graphs with the continuous x-axis, there is an option called explorer.
It allows users to pan and zoom Google charts. It is very handy in case
you have many data points and limited drawing area. Try to set the last 49 days
in the below bubble chart and <b>zoom in</b> and <b>out</b>:
<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="http://gcharts.briskat.com/assets/bubble.html"></iframe>
</div>

<h3>Column chart</h3>
<p>Line chart and column chart are quite simple graphs so
drawing thousand values is pretty quick. You can try animation
option in the following graph.
<div class="embed-responsive embed-responsive-16by9">
  <iframe class="embed-responsive-item" src="http://gcharts.briskat.com/assets/column.html"></iframe>
</div>
</p>

<p>This effect allows to have nicer transitions when loading new graph data.
But it can be used as a technique to delay requests coming out of your UI application,
which means your server can handle more concurrent users.
</p>

<h3>Dashboard</h3>
<p>
Hopefully, we showed that using interactive graphs, you can build pretty
fancy pages. In the end let's see all previous reports together on
<a href="http://gcharts.briskat.com">
one dashboard</a>.
<a href="http://gcharts.briskat.com">
<img src="/img/posts/6_dashboard.png" class="img-responsive" alt="dashboard" align="center">
</a>

The number of points drawn for the top graphs is limited,
but still the responsiveness is pretty good.
You can send us your good or bad user experience to
ask&commat;<img src="/img/logo-s.png" alt="briskat" style="padding-bottom: 8px" width="87" height="46">.com
</p>
