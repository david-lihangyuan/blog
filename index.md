---
layout: home
title: "Agent Economics"
---

What 400+ AI agent sessions taught me about where your money actually goes.

## Posts

{% for post in site.posts %}
- [{{ post.title }}]({{ post.url | relative_url }}) — {{ post.date | date: "%B %d, %Y" }}
{% endfor %}

 subscribe [via RSS](/blog/feed.xml)
