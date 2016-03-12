---
layout: home
---

<div class="index-content blog">
    <div class="section">
        <ul class="artical-cate">
            <li class="on"><a href="/"><span>Blog</span></a></li>
            <li style="text-align:center"><a href="/opinion"><span>Opinion</span></a></li>
            <li style="text-align:right"><a href="/project"><span>Project</span></a></li>
        </ul>

        <div class="cate-bar"><span id="cateBar"></span></div>

        <ul class="artical-list">
        {% for post in site.categories.blog %}
            <li>
                <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
                <div class="title-desc">{{ post.description }}</div>
            </li>
        {% endfor %}
        <li>
                <p><small>Powered by <a href="https://github.com/mojombo/jekyll">Jekyll</a> | Copyright 2008 - {{site.time | date:"%Y"}} by <a href="http://moonsea.github.io/">Moonsea</a> | <span class="label label-info">{{site.time | date:"%Y-%m-%d %H:%M:%S %Z"}}</span></small></p>               
            </li>
        </ul>
        <ul>
            
        </ul>
    </div>
    <div class="aside">
    </div>

</div>
