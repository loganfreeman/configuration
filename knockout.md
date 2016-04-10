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

placeholder
---
```js
  ko.bindingHandlers.placeholder = {
    init: function (element, valueAccessor, allBindingsAccessor) {
      var underlyingObservable = valueAccessor();
      ko.applyBindingsToNode(element, { attr: { placeholder: underlyingObservable } } );
    }
  };
```

datepicker
---
```js
  ko.bindingHandlers.datePicker = {
      init: function (element, valueAccessor, allBindingsAccessor) {
         var value = ko.utils.unwrapObservable(valueAccessor());
         if (value) $(element).datepicker('update', value);
         $(element).change(function() {
            var value = valueAccessor();
            value($(element).val());
         })
      },
      update: function (element, valueAccessor) {
         var value = ko.utils.unwrapObservable(valueAccessor());
         if (value) $(element).datepicker('update', value);
      }
  };
```

bootstrap combobox
---
```js
  ko.bindingHandlers.combobox = {
      init: function (element, valueAccessor, allBindingsAccessor) {
         var options = allBindingsAccessor().dropdownOptions|| {};
         var value = ko.utils.unwrapObservable(valueAccessor());
         var id = (value && value.public_id) ? value.public_id() : (value && value.id) ? value.id() : value ? value : false;
         if (id) $(element).val(id);
         $(element).combobox(options);

          ko.utils.registerEventHandler(element, "change", function () {
            var value = valueAccessor();
            value($(element).val());
          });
      },
      update: function (element, valueAccessor) {
        var value = ko.utils.unwrapObservable(valueAccessor());
        var id = (value && value.public_id) ? value.public_id() : (value && value.id) ? value.id() : value ? value : false;
        if (id) {
          $(element).val(id);
          $(element).combobox('refresh');
        } else {
          $(element).combobox('clearTarget');
          $(element).combobox('clearElement');
        }
      }
  };
```
markup with Former
```php
    {!! Former::select('expense_currency_id')->addOption('','')
            ->data_bind('combobox: expense_currency_id')
            ->label(trans('texts.currency_id'))
            ->data_placeholder(Utils::getFromCache($account->getCurrencyId(), 'currencies')->name)
            ->fromQuery($currencies, 'name', 'id') !!}
```


file path
---
```js
  ko.bindingHandlers.filepath = {
    init: function(element, valueAccessor) {
      var fullPath = ko.utils.unwrapObservable(valueAccessor());
      var filename = fullPath.replace(/^.*[\\\/]/, '');
      $(element).val(filename);
    }
  }
```
