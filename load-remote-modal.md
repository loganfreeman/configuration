```js
    /*
     * --------------------
     * Create a simple way to show remote dynamic modals from the frontend
     * --------------------
     *
     * E.g :
     * <a href='/route/to/modal' class='loadModal'>
     *  Click For Modal
     * </a>
     *
     */
    $(document.body).on('click', '.loadModal, [data-invoke~=modal]', function (e) {

        var loadUrl = $(this).data('href'),
            modalId = $(this).data('modal-id'),
            cacheResult = $(this).data('cache') === 'on';

        // $('#' + modalId).remove();
        $('.modal').remove();
        $('html').addClass('working');

        /*
         * Hopefully this message will rarely show
         */
        setTimeout(function () {
            //showMessage('One second...'); #far to annoying
        }, 750);

        $.ajax({
            url: loadUrl,
            data: {'modal_id': modalId},
            localCache: cacheResult,
            dataType: 'html',
            success: function (data) {
                hideMessage();

                $('body').append(data);

                var $modal = $('#' + modalId);

                $modal.modal({
                    'backdrop': 'static'
                });

                $modal.modal('show');

                $modal.on('hidden.bs.modal', function (e) {
                    // window
                    location.hash = '';
                });

                $('html').removeClass('working');
            }
        }).done().fail(function (data) {
            $('html').removeClass('working');
            showMessage('Whoops!, something has gone wrong.<br><br>' + data.status + ' ' + data.statusText);
        });

        e.preventDefault();
    });
```
