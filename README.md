# Wiki-style links in Jekyll

This repository proves that Jekyll allows you to convert the code below in the HTML below:

    This is a test with [[Testlink 1]] and a [[Testlink 2]] and a [Markdown link](/).

This is a test with [Testlink 1](/testlink1) and a [Testlink 2](/testlink2) and a [Markdown link](/).

You might want this if you want to build a Wiki-styled link system (credits to https://en.wikipedia.org/wiki/Ward_Cunningham).

To do so you need 4 steps. Step 1 is to split the content in such a way that you can create a chunks that only contain one link. I call this the contentarray, or an item. Each items gets split into the link part (itemparts[0]) and the rest (itemparts[1]). Then you need to match all pages against this link part. All matches are stored in the links and urls string, seperated by comma's. Step 2 is to split these strings into arrays. Step 3 is to loop over the links to replace each link in the original content and store it in a new variable, called replacedcontent. This replacedcontent is not yet markdownified, sot the final step is to show the replaced content and markdownify it.

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
Happy coding!
