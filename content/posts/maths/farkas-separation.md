---
author: "Pantelis Sopasakis"
title:  From Separation Theorems to Farkas' Lemma
draft: true
date: 2022-08-30
description: From the Hahn-Banach theorem to the three separating theorems
summary: From the Hahn-Banach theorem to the three separating theorems
math: true
series: ["Mathematix"]
tags: ["Convex Analysis", "Functional Analysis", "Analysis"]
showtoc: false
---

<style>
table {
  border-collapse: collapse;
  width: 100%;
  table-layout: fixed;
}

td, th {

  text-align: left;
  padding: 8px;
  width: 33%
}


</style>

{{< math.inline >}}
{{ if or .Page.Params.math .Site.Params.math }}

<!-- KaTeX -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex@0.11.1/dist/katex.min.css" integrity="sha384-zB1R0rpPzHqg7Kpt0Aljp8JPLqbXI3bhnPWROx27a9N0Ll6ZP/+DiW/UqRcLbRjq" crossorigin="anonymous">
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.11.1/dist/katex.min.js" integrity="sha384-y23I5Q6l+B6vatafAwxRu/0oK/79VlbSz7Q9aiSZUvyWYIYsd+qj+o24G5ZU2zJz" crossorigin="anonymous"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex@0.11.1/dist/contrib/auto-render.min.js" integrity="sha384-kWPLUVMOks5AQFrykwIup5lo0m3iMkkHrD0uJ4H5cjeGihAutqP0yW0J6dpFiVkI" crossorigin="anonymous" onload="renderMathInElement(document.body);"></script>
{{ end }}
{{</ math.inline >}}