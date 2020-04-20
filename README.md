## Match the slug

**Convert this:**

    This is a test with [[testlink1]] and a [[testlink2]] and a [Markdown link](/).

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
        {% assign slug = itemparts[0] | prepend:'/' | append:'/' %}
        {% assign result = site.pages | where: 'url',slug %}
        {% if forloop.index == 2 %}
            {% assign titles = result[0].title %}
        {% else %}
            {% assign titles = titles | append: ',' | append: result[0].title %}
        {% endif %}
    {% endif %}
{% endfor %}

{% assign titlearray = titles | split:',' %}
{% assign linkarray = links | split:',' %}

{% assign replacedcontent = page.content %}
{% for item in linkarray %}
    {% assign linktext = '<a href="' | append: item | append: '">' | append: titlearray[forloop.index0] | append: '</a>' %}
    {% assign bracketlink = '[[' | append: item | append: ']]' %}
    {% assign replacedcontent = replacedcontent | replace: bracketlink,linktext %}
{% endfor %}

{{ replacedcontent | markdownify }}
```
___

## Match the title

**It may be nicer and easier to match the title instead of the url, but the title is less unique, so it could lead to more errors. The code below would convert this:**

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
