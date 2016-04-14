fadein one by one
---
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
