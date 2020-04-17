---
layout: default
title: Learn
---

## {{ page.title }}

Here is a collection of writings, notes and information I find useful.

<div class="posts">
<ul>
{% for post in site.posts %}
	<li>
		<small>{{ post.date | date_to_string }}</small> - <a href="{{ post.url }}">{{ post.title }}</a>
	<hr>
	</li>
{% endfor %}
</ul>
</div>
<br/>
