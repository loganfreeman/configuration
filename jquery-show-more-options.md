```js

    /*
     * ------------------------------------------------------------
     * Toggle hidden content when a.show-more-content is clicked
     * ------------------------------------------------------------
     */
    $(document.body).on('click', '.show-more-options', function (e) {

        var toggleClass = !$(this).data('toggle-class')
            ? '.more-options'
            : $(this).data('toggle-class');


        if ($(this).hasClass('toggled')) {
            $(this).html($(this)
                .data('original-text'));

        } else {

            if (!$(this).data('original-text')) {
                $(this).data('original-text', $(this).html());
            }
            $(this).html(!$(this).data('show-less-text') ? 'Show Less' : $(this).data('show-less-text'));
        }

        $(this).toggleClass('toggled');

        /*
         * ?
         */
        if ($(this).data('clear-field')) {
            $($(this).data('clear-field')).val('');
        }

        $(toggleClass).slideToggle();
        e.preventDefault();
    });
```
