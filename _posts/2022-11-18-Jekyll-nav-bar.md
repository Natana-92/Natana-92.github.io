---
layout: post
title: Jekyll nav bar 분석
subtitle: Jekyll nav bar를 추가하기 위해서 분석사항을 정리합니다.
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [jekyll]
---
# jekyll nav bar 추가 삽질 내용 공유

md 파일 위에는 아래와 같이 layout에 대한 포괄적인 내용을 삽입하게 됩니다.
jekkyll root 폴더의 html 파일을 가져와 렌더링합니다.

```
---
layout: page
title: 주인 소개
subtitle: 주인장을 소개합니다.
---
```


```html
---
layout: base
---
{% include header.html type="page" %}
<div class="{% if page.full-width %} container-fluid {% else %} container-md {% endif %}" role="main">
  <div class="row">
    <div class="{% if page.full-width %} col {% else %} col-xl-8 offset-xl-2 col-lg-10 offset-lg-1 {% endif %}">
      {% if page.before-content %}
        <div class="before-content">
          {% for file in page.before-content %}
            {% include {{ file }} %}
          {% endfor %}
        </div>
      {% endif %}

      {{ content }}

      {% if page.after-content %}
        <div class="after-content">
          {% for file in page.after-content %}
            {% include {{ file }} %}
          {% endfor %}
        </div>
      {% endif %}

      {% include comments.html %}
    </div>
  </div>
</div>

```

위코드의 page의 내용은 _config.yml의변수 내용을 변환하여 사용합니다.

이제 github.io 화면에 진입한 후 처음 보여지는 화면인 home 화면을 보겠습니다.`
```
---
layout: home
title: Welcome my homepage
subtitle: 개발에 관련된 지식을 축적하기 위한 블로그 입니다.
---
```

레이아웃이 home으로 잡혀있는 것을 볼 수 있습니다.
home.html의 내용은 하기와 같습니다.
```javascript
---
layout: page
---

{{ content }}

{% assign posts = paginator.posts | default: site.posts %}

<!-- role="list" needed so that `list-style: none` in Safari doesn't remove the list semantics -->
<ul class="posts-list list-unstyled" role="list">
  {% for post in posts %}
  <li class="post-preview">
    <article>

      {%- capture thumbnail -%}
        {% if post.thumbnail-img %}
          {{ post.thumbnail-img }}
        {% elsif post.cover-img %}
          {% if post.cover-img.first %}
            {{ post.cover-img[0].first.first }}
          {% else %}
            {{ post.cover-img }}
          {% endif %}
        {% else %}
        {% endif %}
      {% endcapture %}
      {% assign thumbnail=thumbnail | strip %}

      {% if site.feed_show_excerpt == false %}
      {% if thumbnail != "" %}
      <div class="post-image post-image-normal">
        <a href="{{ post.url | absolute_url }}" aria-label="Thumbnail">
          <img src="{{ thumbnail | absolute_url }}" alt="Post thumbnail">
        </a>
      </div>
      {% endif %}
      {% endif %}

      <a href="{{ post.url | absolute_url }}">
        <h2 class="post-title">{{ post.title | strip_html }}</h2>

        {% if post.subtitle %}
          <h3 class="post-subtitle">
          {{ post.subtitle | strip_html }}
          </h3>
        {% endif %}
      </a>

      <p class="post-meta">
        {% assign date_format = site.date_format | default: "%B %-d, %Y" %}
        Posted on {{ post.date | date: date_format }}
      </p>

      {% if thumbnail != "" %}
      <div class="post-image post-image-small">
        <a href="{{ post.url | absolute_url }}" aria-label="Thumbnail">
          <img src="{{ thumbnail | absolute_url }}" alt="Post thumbnail">
        </a>
      </div>
      {% endif %}

      {% unless site.feed_show_excerpt == false %}
      {% if thumbnail != "" %}
      <div class="post-image post-image-short">
        <a href="{{ post.url | absolute_url }}" aria-label="Thumbnail">
          <img src="{{ thumbnail | absolute_url }}" alt="Post thumbnail">
        </a>
      </div>
      {% endif %}

      <div class="post-entry">
        {% assign excerpt_length = site.excerpt_length | default: 50 %}
        {{ post.excerpt | strip_html | truncatewords: excerpt_length }}
        {% assign excerpt_word_count = post.excerpt | number_of_words %}
        {% if post.content != post.excerpt or excerpt_word_count > excerpt_length %}
          <a href="{{ post.url | absolute_url }}" class="post-read-more">[Read&nbsp;More]</a>
        {% endif %}
      </div>
      {% endunless %}

      {% if site.feed_show_tags != false and post.tags.size > 0 %}
      <div class="blog-tags">
        <span>Tags:</span>
        <!-- role="list" needed so that `list-style: none` in Safari doesn't remove the list semantics -->
        <ul class="d-inline list-inline" role="list">
          {% for tag in post.tags %}
          <li class="list-inline-item">
            <a href="{{ '/tags' | absolute_url }}#{{- tag -}}">{{- tag -}}</a>
          </li>
          {% endfor %}
        </ul>
      </div>
      {% endif %}

    </article>
  </li>
  {% endfor %}
</ul>

{% if paginator.total_pages > 1 %}
<ul class="pagination main-pager">
  {% if paginator.previous_page %}
  <li class="page-item previous">
    <a class="page-link" href="{{ paginator.previous_page_path | absolute_url }}">&larr; Newer Posts</a>
  </li>
  {% endif %}
  {% if paginator.next_page %}
  <li class="page-item next">
    <a class="page-link" href="{{ paginator.next_page_path | absolute_url }}">Older Posts &rarr;</a>
  </li>
  {% endif %}
</ul>
{% endif %}

```

여기서 메인 홈페이지의 post 내용 및 prev, next 버튼에 대한 내용이 담겨있는 것을 확인할 수 있습니다.
이제 nav bar를 열어 catagory 별로 post를 모아나가봅시다.
tag를 기준으로 post를 보여주면 될 겁니다.

nav.html의 내용을 봅시다.
```javascript
 {%- if link[1].first %}
    <li class="nav-item dropdown">
    <a class="nav-link dropdown-toggle" href="#" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">{{ link[0] }}</a>
    <div class="dropdown-menu dropdown-menu-right" aria-labelledby="navbarDropdown">
        {%- for childlink in link[1] -%}
        {%- for linkparts in childlink %}
            <a class="dropdown-item" href="{{ linkparts[1] | relative_url }}">{{ linkparts[0] }}</a>
        {%- endfor -%}
        {%- endfor %}
    </div>
    </li>
{% else %}
```
nav-