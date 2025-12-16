---
permalink: /
title: ""
excerpt: ""
author_profile: true
redirect_from: 
  - /about/
  - /about.html
---

{% if site.google_scholar_stats_use_cdn %}
{% assign gsDataBaseUrl = "https://cdn.jsdelivr.net/gh/" | append: site.repository | append: "@" %}
{% else %}
{% assign gsDataBaseUrl = "https://raw.githubusercontent.com/" | append: site.repository | append: "/" %}
{% endif %}
{% assign url = gsDataBaseUrl | append: "google-scholar-stats/gs_data_shieldsio.json" %}
<!-- ========================================================================================================================================== -->
{% include_relative about/about-me.md %}
{% include_relative about/news.md %}
{% include_relative about/publications.md %}
{% include_relative about/honors.md %}
{% include_relative about/educations.md %}
{% include_relative about/teaching.md %}
{% include_relative about/hobbies.md %}
{% include_relative about/reading-notes.md %}
<!-- ========================================================================================================================================== -->
<style>
/* 只影响正文内容区的超链接 */
.page__content p a,
.page__content li a,
.post-content p a,
.post-content li a {
  color: rgb(125,181,168);
  background-color: rgba(125,181,168, 0.00);
  padding: 2px 4px;
  border-radius: 4px;
  text-decoration: none;
  transition: all 0.2s ease;
}

.page__content p a:hover,
.page__content li a:hover,
.post-content p a:hover,
.post-content li a:hover {
  color: rgb(105,161,148);
  background-color: rgba(125,181,168, 0.12);
}
</style>
<!-- ========================================================================================================================================== -->
