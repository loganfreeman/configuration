fadein one by one
---
javascript
```js
$(document).ready(function() {
           fade_in_next();
        });
var i = 0;
function fade_in_next() {
  $("ul li:hidden:first").fadeIn("slow", function() {
    i=i+1;
    var result = setTimeout(fade_in_next, 500);
    if(i==8)
    {
      $('.submit_button_main').removeClass('hide');
    }    
  });
}
```
css
```css
.reset_form ul li {
    list-style: none;
    float: left;
    margin-left: 10px;
    font-size: 18px !important;
    margin-top: 10px;
    width: 100%;
    display: none;
}
```
diplay control when hovering over
---
```js
      // Displaying widget controls on hover
      $('.chart-header').hover(
        function () {
          $(this).find('.chart-controls').fadeIn(300);
        },
        function () {
          $(this).find('.chart-controls').fadeOut(300);
        }
      );
```
markup
```html
            <div class="chart-header">
              <div class="row">
                <div class="col-md-12 text-center header">
                  {{ slice.slice_name }}
                </div>
                <div class="col-md-12 chart-controls">
                  <div class="pull-left">
                    <a title="Move chart" data-toggle="tooltip">
                      <i class="fa fa-arrows drag"></i>
                    </a>
                    <a class="refresh" title="Force refresh data" data-toggle="tooltip">
                      <i class="fa fa-repeat"></i>
                    </a>
                  </div>
                  <div class="pull-right">
                    {% if slice.description %}
                      <a title="Toggle chart description">
                        <i class="fa fa-info-circle slice_info" slice_id="{{ slice.id }}" title="{{ slice.description }}" data-toggle="tooltip"></i>
                      </a>
                    {% endif %}
                    <a href="{{ slice.edit_url }}" title="Edit chart" data-toggle="tooltip">
                      <i class="fa fa-pencil"></i>
                    </a>
                    <a href="{{ slice.slice_url }}" title="Explore chart" data-toggle="tooltip">
                      <i class="fa fa-share"></i>
                    </a>
                    <a class="remove-chart" title="Remove chart from dashboard" data-toggle="tooltip">
                      <i class="fa fa-close"></i>
                    </a>
                  </div>
                </div>

              </div>
            </div>
```
alert dismissable
---
```coffee
:coffeescript
  window.setTimeout (->
    $(".alert-dismissable").fadeTo(500, 0).slideUp 500, ->
      $(this).remove()
      return

    return
  ), 4000
```
