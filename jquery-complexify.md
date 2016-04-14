js
```js
    $("#password").complexify({
        minimumChars: 9,
        strengthScaleFactor: 0.6
    }, function(valid, complexity) {
        if (!valid) {
            $('#progress').css({
                'width': complexity + '%'
            }).removeClass('progressbarValid').addClass('progressbarInvalid');
        } else {
            $('#progress').css({
                'width': complexity + '%'
            }).removeClass('progressbarInvalid').addClass('progressbarValid');
        }

    });
```
markup
```html
  <div class="span4 progress_data" id="progressbar"><div id="progress"> </div></div>
```
css
```css
#progressbar {
    width: 259px;
    height: 33px;
    display: block;
    border-left: 1px solid #ccc;
    border-right: 1px solid #ccc;
    border-top: 1px solid #ccc;
    border-bottom: 1px solid #ccc;
    border-top-right-radius: 8px;
    border-top-left-radius: 8px;
    border-bottom-right-radius: 8px;
    border-bottom-left-radius: 8px;
    overflow: hidden;
    background-color: white;
}
#progress {
    display: block;
    height: 100px;
    width: 0%;
}
.progressbarValid {
    background-color: green;
    background-image: -o-linear-gradient(-90deg, #8AD702 0%, #389100 100%);
    background-image: -moz-linear-gradient(-90deg, #8AD702 0%, #389100 100%);
    background-image: -webkit-linear-gradient(-90deg, #8AD702 0%, #389100 100%);
    background-image: -ms-linear-gradient(-90deg, #8AD702 0%, #389100 100%);
    background-image: linear-gradient(-90deg, #8AD702 0%, #389100 100%);
}
.progressbarInvalid {
    background-color: red;
    background-image: -o-linear-gradient(-90deg, #F94046 0%, #92080B 100%);
    background-image: -moz-linear-gradient(-90deg, #F94046 0%, #92080B 100%);
    background-image: -webkit-linear-gradient(-90deg, #F94046 0%, #92080B 100%);
    background-image: -ms-linear-gradient(-90deg, #F94046 0%, #92080B 100%);
    background-image: linear-gradient(-90deg, #F94046 0%, #92080B 100%);
}
```
