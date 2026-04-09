---
layout: ai-agent
title: AI 에이전트 활용
permalink: /ai-agent/
---

# Gemini CLI 활용법

<div class="section-index">
    <hr class="panel-line">
    {% assign filtered_posts = site.posts | where_exp: "item", "item.categories contains 'AI 에이전트 활용'" %}
    {% for post in filtered_posts  %}        
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>{% endfor %}
</div>
