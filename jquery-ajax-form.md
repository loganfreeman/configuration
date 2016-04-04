```js
    /*
     * --------------------
     * Ajaxify those forms
     * --------------------
     *
     * All forms with the 'ajax' class will automatically handle showing errors etc.
     *
     */
    $('form.ajax').ajaxForm({
        delegation: true,
        beforeSubmit: function (formData, jqForm, options) {

            $(jqForm[0])
                .find('.error.help-block')
                .remove();
            $(jqForm[0]).find('.has-error')
                .removeClass('has-error');

            var $submitButton = $(jqForm[0]).find('input[type=submit]');
            toggleSubmitDisabled($submitButton);


        },
        uploadProgress: function (event, position, total, percentComplete) {
            $('.uploadProgress').show().html('Uploading Images - ' + percentComplete + '% Complete...    ');
        },
        error: function (data, statusText, xhr, $form) {

            // Form validation error.
            if (422 == data.status) {
                processFormErrors($form, $.parseJSON(data.responseText));
                return;
            }

            showMessage('Whoops!, it looks like something went wrong on our servers.\n\
                   Please try again, or contact support if the problem persists.');

            var $submitButton = $form.find('input[type=submit]');
            toggleSubmitDisabled($submitButton);

            $('.uploadProgress').hide();
        },
        success: function (data, statusText, xhr, $form) {

            switch (data.status) {
                case 'success':

                    if ($form.hasClass('reset')) {
                        $form.resetForm();
                    }

                    if ($form.hasClass('closeModalAfter')) {
                        $('.modal, .modal-backdrop').fadeOut().remove();
                    }

                    var $submitButton = $form.find('input[type=submit]');
                    toggleSubmitDisabled($submitButton);

                    if (typeof data.message !== 'undefined') {
                        showMessage(data.message);
                    }

                    if (typeof data.runThis !== 'undefined') {
                        eval(data.runThis);
                    }

                    if (typeof data.redirectUrl !== 'undefined') {
                        window.location = data.redirectUrl;
                    }

                    break;

                case 'error':
                    processFormErrors($form, data.messages);
                    break;

                default:
                    break;
            }

            $('.uploadProgress').hide();
        },
        dataType: 'json'
    });
```
