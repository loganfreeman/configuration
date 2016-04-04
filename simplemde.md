markup
---
```html
      <div class="form-group custom-theme">
          {!! Form::label('description', 'Event Description', array('class'=>'control-label')) !!}
          {!!  Form::textarea('description', Input::old('description'),
                      array(
                      'class'=>'form-control  editable',
                      'rows' => 5
                      ))  !!}
      </div>
                        
```
javascript
---
```js
$(function() {
        var simplemde = new SimpleMDE({
            element: $(".ediatble")[0],
            spellChecker: false,
            status: false
        });
});
