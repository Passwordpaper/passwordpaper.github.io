---
layout: home
---

# This site is intentionally bare, but welcome!

{% for post in site.posts %}
  [{{ post.title }}]({{ site.baseurl }}{{ post.url }})
  
{% endfor %}
