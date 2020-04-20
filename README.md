# Wiki-style links in Jekyll

**This repository proves that it is relatively easy to convert this:**

    This is a test with [[Testlink 1]] and a [[Testlink 2]] and a [Markdown link](/).

**... into this:**

This is a test with [Testlink 1](/testlink1) and a [Testlink 2](/testlink2) and a [Markdown link](/).

**... using this code:**

```
{% assign contentarray = page.content | split:'[[' %}
{% for item in contentarray %}
    {% if forloop.index > 1 %}
        {% assign itemparts = item | split:']]' %}
        {% if forloop.index == 2 %}
            {% assign links = itemparts[0] %}
        {% else %}
            {% assign links = links | append: ',' | append: itemparts[0] %}
        {% endif %}
        {% assign result = site.pages | where: 'title',itemparts[0] %}
        {% if forloop.index == 2 %}
            {% assign urls = result[0].url %}
        {% else %}
            {% assign urls = urls | append: ',' | append: result[0].url %}
        {% endif %}
    {% endif %}
{% endfor %}

{% assign urlarray = urls | split:',' %}
{% assign linkarray = links | split:',' %}

{% assign replacedcontent = page.content %}
{% for item in linkarray %}
    {% assign linktext = '<a href="' | append: urlarray[forloop.index0] | append: '">' | append: item | append: '</a>' %}
    {% assign bracketlink = '[[' | append: item | append: ']]' %}
    {% assign replacedcontent = replacedcontent | replace: bracketlink,linktext %}
{% endfor %}

{{ replacedcontent | markdownify }}
```
        
**Happy coding!**
