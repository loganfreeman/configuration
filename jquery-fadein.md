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
