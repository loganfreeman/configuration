navbar
---
```html
<nav class="navbar navbar-default navbar-inverse navbar-static-top" role="navigation">
    <div class="container">
        <div class="navbar-header">
            <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
            </button>
            <a class="navbar-brand" href="#">Grav</a>
        </div>
        <div class="navbar-collapse collapse">
            <ul class="nav navbar-nav navbar-right">
                {% for page in pages.children %}
                {% if page.visible %}
                {% set current_page = (page.active or page.activeChild) ? 'active' : '' %}
                <li class="{{ current_page }}"><a href="{{ page.url }}">{{ page.menu }}</a></li>
                {% endif %}
                {% endfor %}
            </ul>
        </div>
    </div>
</nav>
```
