---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Home
---

<div class="container">
<div class="row">
<div class="col-md-10">
{% include postlist.html %}
</div>

<div class="col-md-2">
{% include categories.html %}
{% include tagcloud.html %}
</div>
</div>
</div>
