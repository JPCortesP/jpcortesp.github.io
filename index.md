---
layout: default
title: Inicio
---

{% if site.posts.size > 0 %}
	{% for post in site.posts limit:10 %}
		<article>
			<h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
			<p><em>{{ post.date | date: "%d/%m/%Y" }}</em></p>
			{{ post.excerpt }}
			<p><a href="{{ post.url | relative_url }}">Seguir leyendo</a></p>
		</article>
	{% endfor %}
{% else %}
	<p>No hay publicaciones todavia.</p>
{% endif %}
