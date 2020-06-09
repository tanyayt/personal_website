---
layout: post
title:  "Build an Interactive Calendar using Tableau"
tags: tableau
---
<iframe frameborder="0" src="https://public.tableau.com/views/AirbnbRentalAvailabilityandPricingCalendar/Calendar?:display_count=y&:origin=viz_share_link?:embed=yes&:display_count=yes&:showVizHome=no" width = "800px" height="900px" scrolling='auto' allow></iframe>
Yes, you can build a calendar heatmap using Tableau! 

1.  Create a calculated field [week index]

    <img src ="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/tableau_calendar_week_index.PNG?raw=true" width="350px"> 
 
2.  Drag `WEEKDAY(Date)` to columns; drag `MONTH(Date)` and `week_index` to Rows; drag quantitative field of your choice to Color; Drag any additional columns to tooltip and/or detail as needed 

    <img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/tableau_calendar_row_col.PNG?raw=true" height ="300px"> 

3.  You might have noticed the original dashboard also has a hover-over effect so you can see how a second measure (in this case "rental rate" changes in the selected week). To create this effect, build a line chart with `DAY[date]` on Columns, and quantitative measures on rows. Add labels as needed and any color schemes as needed. 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/tableau_calendar_price_chart.PNG?raw=true" height="300px"> 

4.  Lastly, insert the sheet to the calendar sheet's tool tip, set the filter equal to, in this case, "week number" 

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/tableau_calendar_weeknumber.PNG?raw=true" height="80px" align ="left">

<img src="https://github.com/tanyayt/tanyayt.github.io/blob/master/images/tableau_calendar_tooltip_sheet.PNG?raw=true" height= "300px" align="left">
