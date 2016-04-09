tooltip
---
binding
```js
  ko.bindingHandlers.tooltip = {
    init: function(element, valueAccessor) {
        var local = ko.utils.unwrapObservable(valueAccessor()),
        options = {};

        ko.utils.extend(options, ko.bindingHandlers.tooltip.options);
        ko.utils.extend(options, local);

        $(element).tooltip(options);

        ko.utils.domNodeDisposal.addDisposeCallback(element, function() {
            $(element).tooltip("destroy");
        });
    },
    options: {
        placement: "bottom",
        trigger: "hover"
    }
  };
}
```
markup
```html
<span style="vertical-align:text-top;color:red" class="fa fa-exclamation-triangle"
      data-bind="visible: $data.email_error, tooltip: {title: $data.email_error}"></span>
```                               
