---
layout: post
title:  "Embedding Tableau Dashboards"
tags: web-development tableau
---

This site is built using [Jekyll Now repository](https://github.com/barryclark/jekyll-now) which allows me to use Markdown files to publish pages and blog posts. Since I have published a handful of Tableau Dashboards on [Tableau Public](https://public.tableau.com/profile/tanya.t3433#!/), I would like to show case those on my personal website too

## The Process

### 1. Revise Tableau Dashboard 

My orignal [interactive resume](https://public.tableau.com/views/TanyaTan-Resume/AboutTanyaTan?:retry=yes&:display_count=y&publish=yes&:origin=viz_share_link) is designed for at least 1000 px wide and has redundant information on the left-hand size (e.g. profile picture and contact information). I revised the dashboard dimensions and published a stand-alone [vertical version](https://public.tableau.com/profile/tanya.t3433#!/vizhome/TanyaTan-Resume-vertical/AboutTanyaTan). The new dimension is 800x900 which works well for my website. 

### 2.Embed Code

#### A Failed attempt

I copied the embed code directly from Tableau Public, which looks like this: 

```
<div class='tableauPlaceholder' id='viz1587769492356' style='position: relative'><noscript><a href='#'><img alt=' ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ta&#47;TanyaTan-Resume-vertical&#47;AboutTanyaTan&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='TanyaTan-Resume-vertical&#47;AboutTanyaTan' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ta&#47;TanyaTan-Resume-vertical&#47;AboutTanyaTan&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1587769492356');                    var vizElement = divElement.getElementsByTagName('object')[0];                    if ( divElement.offsetWidth > 800 ) { vizElement.style.width='800px';vizElement.style.height='927px';} else if ( divElement.offsetWidth > 500 ) { vizElement.style.width='800px';vizElement.style.height='927px';} else { vizElement.style.width='100%';vizElement.style.height='1077px';}                     var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>

```
For some reason, this approach doesn't allow me to adjust the dimensions. And honestly, not knowing which line of code is doing which makes me anxious. 

#### The Go-To-Approach

I decided to use <iframe> instead (inspired by [Data Viz For ALL](https://datavizforall.org/iframe-tableau.html) and [San Wang](https://san-wang.github.io/blog/Embed-Tableau-dashboard-into-github-page-post/) ). I added the following codes to the Markdown file, as plain text. No automatic indentation or code blocks. They should appear like plain body text in the markdown but will be rendered correctly on the site. 

```
<iframe frameborder="0" src="https://public.tableau.com/views/TanyaTan-Resume-vertical/AboutTanyaTan?:embed=yes&:display_count=yes&:showVizHome=no" width = '800' height = '900' scrolling='auto' allow></iframe>

```
iframe parameters: 

..* src: URL, specifies the link. Be sure to use the link that has '/views' instead of the direct address from the address bar (with '/profile')
..* width/height: adjust width/height in pixels
..* frameborder: width of frameborder in pixels; default is 1
..* loading = eager or lazy, determine whether the iframe is loaded immediatel (eager) or until the page reaches certain point. 
..* marginheight
..* marginwidth
..* scrolling: takes 'auto', 'yes', and 'no'

### Final Product

<iframe frameborder="0" src="https://public.tableau.com/views/TanyaTan-Resume-vertical/AboutTanyaTan?:embed=yes&:display_count=yes&:showVizHome=no" width = '800' height = '900' scrolling='auto' allow></iframe>



