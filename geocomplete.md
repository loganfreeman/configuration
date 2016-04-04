markup
---
```html
{!! Form::open(array('url' => route('postCreateEvent'), 'class' => 'ajax gf')) !!}
    <div class="form-group address-automatic">
        {!! Form::label('name', 'Venue Name', array('class'=>'control-label required ')) !!}
        {!!  Form::text('venue_name_full', Input::old('venue_name_full'),
                    array(
                    'class'=>'form-control geocomplete location_field',
                    'placeholder'=>'E.g: The Crab Shack'
                    ))  !!}

                <!--These are populated with the Google places info-->
        <div>
            {!! Form::hidden('formatted_address', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('street_number', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('country', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('country_short', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('place_id', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('name', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('location', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('postal_code', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('route', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('lat', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('lng', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('administrative_area_level_1', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('sublocality', '', ['class' => 'location_field']) !!}
            {!! Form::hidden('locality', '', ['class' => 'location_field']) !!}
        </div>
        <!-- /These are populated with the Google places info-->
    </div>
    
{!! Form::close() !!}
```
javascript
---
```js
$(function() {
        try {
            $(".geocomplete").geocomplete({
                    details: "form.gf",
                    types: ["geocode", "establishment"]
                }).bind("geocode:result", function(event, result) {
                    console.log(result);
            }, 1000);

        } catch (e) {
            console.log(e);
        }
});
```
