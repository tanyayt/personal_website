---
title: "Search"
permalink: /search/
layout: page
---

<!-- Html Elements for Search -->

<div id="search-container" height="100px">
<input type="text" id="search-input" placeholder="Search...">
<ul id="results-container"></ul>
</div>

<!-- Script pointing to search-script.js -->

<script src="/js/search-jekyll.js" type="text/javascript"></script>

<!-- Configuration -->

<script>
SimpleJekyllSearch({
  searchInput: document.getElementById('search-input'),
  resultsContainer: document.getElementById('results-container'),
  json: '/search.json'
})
</script>


<h2>Tags</h2>
{% assign tags = site.tags | sort %}
{% for tag in tags %}
 <span class="site-tag">
    <a href="/tag/{{ tag | first | slugify }}/"
        style="font-size: {{ tag | last | size  |  times: 4 | plus: 80  }}%">
            {{ tag[0] | replace:'-', ' ' }} ({{ tag | last | size }})
    </a>
</span>
{% endfor %}


