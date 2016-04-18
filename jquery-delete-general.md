```js
    /*
     * -------------------------------------------------------------
     * Simple way for any type of object to be deleted.
     * -------------------------------------------------------------
     *
     * E.g markup:
     * <a data-route='/route/to/delete' data-id='123' data-type='objectType'>
     *  Delete This Object
     * </a>
     *
     */
    $('.deleteThis').on('click', function (e) {

        /*
         * Confirm if the user wants to delete this object
         */
        if ($(this).data('confirm-delete') !== 'yes') {
            $(this).data('original-text', $(this).html()).html('Click To Confirm?').data('confirm-delete', 'yes');

            var that = $(this);
            setTimeout(function () {
                that.data('confirm-delete', 'no').html(that.data('original-text'));
            }, 2000);

            return;
        }

        var deleteId = $(this).data('id'),
            deleteType = $(this).data('type'),
            route = $(this).data('route');

        $.post(route, deleteType + '_id=' + deleteId)
            .done(function (data) {

                if (typeof data.message !== 'undefined') {
                    showMessage(data.message);
                }

                switch (data.status) {
                    case 'success':
                        $('#' + deleteType + '_' + deleteId).fadeOut();
                        break;
                    case 'error':
                        /* Error */
                        break;

                    default:
                        break;
                }
            }).fail(function (data) {
            showMessage(Attendize.GenericErrorMessages);
        });

        e.preventDefault();
    });
```
ask confirmation
---
```js
// Mock the DELETE form requests.
$('[data-method]').not(".disabled").append(function() {
    var methodForm = "\n";
    methodForm += "<form action='" + $(this).attr('href') + "' method='POST' style='display:none'>\n";
    methodForm += "<input type='hidden' name='_method' value='" + $(this).attr('data-method') + "'>\n";
    methodForm += "<input type='hidden' name='_token' value='" + $('meta[name=token]').attr('content') + "'>\n";
    methodForm += "</form>\n";
    return methodForm;
})
    .removeAttr('href')
    .on('click', function() {
        var button = $(this);

        if (button.hasClass('confirm-action')) {
            askConfirmation(function() {
                button.find("form").submit();
            });
        } else {
            button.find("form").submit();
        }
    });
        
function askConfirmation(callback) {
    swal({
        type: "warning",
        title: "Confirm your action",
        text: "Are you sure you want to do this?",
        confirmButtonText: "Yes",
        confirmButtonColor: "#FF6F6F",
        showCancelButton: true
    }, function() {
        callback();
    });
}
```
