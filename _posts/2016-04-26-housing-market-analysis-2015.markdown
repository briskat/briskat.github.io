---
layout: post
title: Housing Market Graphs and Maps - 2015 vs. 2007
excerpt: In 2007, the U.K. experienced a housing bubble burst. In this post, we are conducting an analysis of the England and Wales housing market before and after 2007 untill 2015. 
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vlad Makovsky</a>
tags: opendata
hashtag: opendata, ukpropertyprices, housingbubble
thumbups: 
image: 11_ukcrash.png
---
In 2007, the U.K. experienced a housing bubble burst.
In this post, we are conducting an analysis of the <b>England</b> and <b>Wales</b> housing market before and after 2007 untill 2015. 
Graphs and heat maps are generated from <a href="http://house.briskat.com">HousePAd</a>
application using Land registry public dataset. This dataset includes nearly all
<b>residential property transactions</b> in England and Wales since 1995 (Scotland and N. Ireland are not publicly available).
We show raw, no inflation-adjusted numbers.

## 1995 - 2007 insane property price growth
Every year from 1996 to 2007, the average property price grew
between 5% and 16%. In this period, all postcode areas multiplied the
average price more than 2.5 times. The area with the lowest
price growth, Durham, grew by 167%. It went from £50k
in 1995 to £135k in 2007. The area with the highest price growth
- East London - grew by 355% - from £61k in 1995 to £278k in 2007.

<h3 class="text-center"><b>House price percentage change by postcode area - 2007 vs. 1995</b></h3>
![England house prices percentage growth from 1995 to 2007](/img/posts/11_growth07.png){: .img-responsive .center-block }

## 2007 market crash
After 12 years of growth, the average property price peaked at £229k in August 2007.
For the next one and half years, it stagnated or it was on a decline:

<h3 class="text-center"><b>Monthly average house price from 1995 to 2015 </b></h3>
<a href="http://house.briskat.com/?tab=protime&yr=" target="_blank">
![Average house prices in England and Wales from 1995 to 2015](/img/posts/11_avg_mo.png){: .img-responsive .center-block }
</a>

While the price drop was relatively small, the drop in sales volumes
was much more significant. Total house sales 
fell by 49% between 2007 and 2008. It is clearly shown in the following heat map:

![Heat map of sales volumes in England and Wales between 2007 and 2008](/img/posts/11_map_volume.png){: .img-responsive .center-block }

In the five-year period following 2007, the sales volumes were down by 47%:

<table class="table table-condensed table-hover">
<thead>
    <tr class="info" style="border-top: inset">
        <th>Year/Period</th><th>Property Transactions</th>
    </tr>
</thead>
<tbody>
    <tr> <td>2003 - 2007</td><td>6.2M</td> </tr>
    <tr style="border-bottom: inset"> <td>2008 - 2012</td><td>3.3M (<b>- 47%</b>)</td> </tr>
</tbody>
</table>

To get a better picture of changes during the 2007 housing market crash, we can correlate
house price percentage change and percentage change in housing transactions
(bigger circle means more transactions):

<h3 class="text-center"><b>Correlation of house price % change and % change in housing transactions<br/>by postcode area - 2008 vs. 2007</b><br/><small><i>click in the picture</i></small></h3>
<a href="http://house.briskat.com/?tab=provol&yr=2008" target="_blank">
![England average house prices percentage change correlated to percentage change in housing transactions, 2008 versus 2007](/img/posts/11_yoy07vs08.png){: .img-responsive .center-block }
</a>

From the graph above, we can see that while transactions were down by tens of percents in all areas
the average property price dropped by 7% at most. Eventually, the price was still on the rise in many other areas.
It shows the housing market resilience. Although the transaction volumes fell off a cliff
properties were still holding their value.

## Sales volumes recovery

A number of transactions for a month was above 80k for most of the year
from 1997 to 2007. After 2007, this number wasn't reached until August 2013:

<h3 class="text-center"><b>Number of property transactions by month</b></h3>
<a href="http://house.briskat.com/?tab=protime&yr=" target="_blank">
![England number of property transactions by month from 1995 to 2015](/img/posts/11_sales_vol_mo.png){: .img-responsive .center-block }
</a>

A number of transactions in 2013 and in 2014 replicated the numbers from 1995 and 1996 but
were still much lower than pre-bubble numbers. 2015 transaction levels
were still 25% below the 2007 levels:

<h3 class="text-center"><b>Number of property transactions by year</b></h3>
<a href="http://house.briskat.com/?tab=protime&yr=" target="_blank">
![Volumes of housing transactions in England by month from 1995 to 2015](/img/posts/11_sales_vol_yr.png){: .img-responsive .center-block }
</a>

## Comparing 2015 to 2007
From 2007 to 2015, the average house price changed quite differently based on postcode areas:

<h3 class="text-center"><b>House price percentage change by postcode area - 2015 vs. 2007</b></h3>
![England house prices percentage change by postcode area, 2007-2015](/img/posts/11_growth15.png){: .img-responsive .center-block }

Not surprisingly, the top 8 fastest growing areas are from London, with East London closing. 
On the other hand, more than half of the areas didn't
grow by more than 10%. A few areas were still below 2007 price levels (and we used the raw numbers).
When we display the previous report on a heat map, we can see the growth is geographically
linked (there are no lines drawn between the areas so the map looks smoother).
For a more detailed view, we also added heat map by postcode district:

![Heat map of England house prices growth 2007 compared to 2015](/img/posts/11_map_yoy.png){: .img-responsive .center-block }

While the prices kept growing in Greater London and adjacent areas, the areas in northern England and in
Wales were on or below 2007 price levels.

The correlation graph reveals the relation between house price percentage change and percentage change in transactions:

<h3 class="text-center"><b>Correlation of house price % change and % change in transactions<br/>by postcode area - 2015 vs. 2007</b></h3>
![England average house prices percentage change correlated to percentage change in transactions, 2015 versus 2007](/img/posts/11_yoy07vs15.png){: .img-responsive .center-block }

To see how the housing market changed from 2008 to 2015 in relation to 2007 we can combine previous correlation graphs into one:

<h3 class="text-center"><b>Correlations - <span style="color: #ffa500;">2015</span> and <span style="color: #fe3232;">2008</span> compared to 2007</b></h3>
![England house price bubble of 2007 compared to 2008 and 2015 house prices and sales volumes](/img/posts/11_yoy_sum.png){: .img-responsive .center-block }

## Where are we?

Many areas experienced a price correction of unsustainable price growth and
their average prices was stagnating since 2007. The sales volumes in 2015 are still one-quarter below
the volumes during the bubble peak but back on pre-crash transaction volumes.

London and its adjacent areas were influenced by the bubble
much less than the rest of the country and continue
in prices growth. The question is, for how long - only time will tell.

<i><small>Images license: © <a href="http://house.briskat.com">house.briskat.com</a>, Land Registry © Crown copyright 2016</small></i>
