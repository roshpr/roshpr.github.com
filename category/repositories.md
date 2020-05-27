---
layout: category
title: Repositories
category: repo
---

List of all repositories and articles related to private and public clouds.

{% for repository in roshpr.github.public_repositories %}
  * [{{ repository.name }}]({{ repository.html_url }})
{% endfor %}
