---
layout: post
title: One Little JS Animation That Made My Day
excerpt: This animation made my day.
author: <a href="http://cz.linkedin.com/pub/vladimir-makovsky/2a/141/141">Vladimir Makovsky</a>
tags: google&#32;charts interactive&#32;analytic
hashtag: googlecharts, houseprices, analytics
image: 9_btn.png
---
<script type="text/javascript" src="https://www.google.com/jsapi"></script>
<script type="text/javascript">
google.load('visualization', '1.0', {'packages':['corechart']});
google.setOnLoadCallback(initMe);

var GS;
var Year;

function initMe() {
	GS=new google.visualization.BubbleChart(document.getElementById('bubble_div'));
	$("#play_btn").click(function() {
		Year = 2010;
		$("#play_btn").hide();
		setTimeout(refresh, 1100);
	});
}

function switchColumns(jsonData,header,fields) {
	var body = $.map(jsonData, function(n,i){
				   var row = new Array();
				   row.push(n[0]);
				   for ( var idx in fields ) {
					   row.push(Math.round(n[fields[idx]]));
				   }
				   return [row];
			   });

	return header.concat(body);
}

function drawGraph(Graph,Year) {
		var Data = $.ajax({
			url: "http://house.briskat.com/api/p/lb_lruk/r/area?pc=1&yr=" + Year,
			dataType: "json",
			async: false
		}).responseJSON;

		var Table = google.visualization.arrayToDataTable(switchColumns(
						Data,
						[['Post Code', 'Average price £','Avg price changed YoY (%)','Properties sold','']],
						[2,3,1,1]
					));
		Graph.draw(Table,
				   { title: 'London property prices, ' + Year,
					 hAxis: {title: 'Average price paid £',
							  viewWindowMode:'explicit',
							  viewWindow:{
								max: 1100000,
								min: 200000
							  }
				    },
					 vAxis: {title: 'Average price changed YoY in %',
					  viewWindowMode:'explicit',
					  viewWindow:{
						max:30,
						min:-30
					  }
					 },
					 animation:{
						 "startup": true,
						  duration: 1000,
						  easing: 'inAndOut'
					},
					'height':500,
				  });
}

function refresh() {
	drawGraph(GS,Year);
	if ( Year < 2016 ) {
		Year = Year + 1;
		setTimeout(refresh, 1500);
	} else {
		$("#play_btn").show();
	}
}
</script>
<p>The human brain is limited. If you need to find all differences
in two pictures it takes a very long time. An animation is
very effective way to show the differences in data over
the time. Long story short, <b>press the button</b> below:
</p>
<p class="text-center">
	<div class="text-center">
    <div id="bubble_div"></div>
    <button id="play_btn" type="button" class="btn btn-success btn-lg"><span class="glyphicon glyphicon-play" aria-hidden="true"></span></button>
	</div>
</p>
<p>This bubble graph shows an average price of properties in London
in a particular year and a postcode area. X-axis shows an average property price,
Y-axis Yoy % growth and size of the circle shows the number of properties sold
in a particular area. By making an animated transition between the years, you
immediately spot the difference over the time.
</p>

<p>Now you may understand my joy. Because making
such animation is nowadays quite easy. Google charts allow
graph animation by using 'animation' option. So
all you need is to have a fast backend which
responds timely to your requests.
</p>

<p>You can play around with the script on <a href="http://jsfiddle.net/briskat/udve84nd">
jsfiddle</a>. If you want to see more options (there are over 1M of combinations,
the data table has 20M rows) try our <a href="http://house.briskat.com?tab=2">live dashboard</a>. Enjoy!
</p>
