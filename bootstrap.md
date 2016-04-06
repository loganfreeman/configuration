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
Bootstrap 3 responsive centered columns
---
This simple css code centers the columns (.col-centered) inside the row (.row-centered).
```css
/* centered columns styles */
.row-centered {
    text-align:center;
}
.col-centered {
    display:inline-block;
    float:none;
    /* reset the text-align */
    text-align:left;
    /* inline-block space fix */
    margin-right:-4px;
}
```
You can also set a min-width, a max-width or a fixed width to the columns.
```css
.col-fixed {
    /* custom width */
    width:320px;
}
.col-min {
    /* custom min width */
    min-width:320px;
}
.col-max {
    /* custom max width */
    max-width:320px;
}
```
The markup
```html
<div class="container">
    <div class="row row-centered">
        <div class="col-xs-6 col-centered"></div>
        <div class="col-xs-6 col-centered"></div>
        <div class="col-xs-3 col-centered"></div>
        <div class="col-xs-3 col-centered"></div>
        <div class="col-xs-3 col-centered"></div>
    </div>
</div>
```
