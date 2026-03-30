---
layout: gitblog
permalink: /gitblog/
---

# 깃블로그운영

<div class="section-index">
    <hr class="panel-line">
    {% for post in site.categories['깃블로그운영']  %}        
    <div class="entry">
    <h5><a href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a></h5>
    <p>{{ post.description }}</p>
    </div>{% endfor %}
</div>
