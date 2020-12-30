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
