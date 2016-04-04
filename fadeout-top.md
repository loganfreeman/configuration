```js

    /* Scroll to top */
    $(window).scroll(function () {
        if ($(this).scrollTop() > 100) {
            $('.totop').fadeIn();
        } else {
            $('.totop').fadeOut();
        }
    });

    $(".totop").click(function () {
        $("html, body").animate({
            scrollTop: 0
        }, 200);
    });
```
