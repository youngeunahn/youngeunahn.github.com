---
layout: gitblog
permalink: /gitblog/
---

# 깃블로그운영

<div class="section-index">
    <hr class="panel-line">
    {% for gitblog in site.gitblog  %}        
    <div class="entry">
    <h5><a href="{{ gitblog.url | prepend: site.baseurl }}">{{ gitblog.title }}</a></h5>
    <p>{{ gitblog.description }}</p>
    </div>{% endfor %}
</div>
