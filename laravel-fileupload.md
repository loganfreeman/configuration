file processing
---
```php

        if ($request->hasFile('event_image')) {
            $path = public_path().'/'.config('attendize.event_images_path');
            $filename = 'event_image-'.md5(time().$event->id).'.'.strtolower($request->file('event_image')->getClientOriginalExtension());

            $file_full_path = $path.'/'.$filename;

            $request->file('event_image')->move($path, $filename);

            $img = Image::make($file_full_path);

            $img->resize(800, null, function ($constraint) {
                $constraint->aspectRatio();
                $constraint->upsize();
            });

            $img->save($file_full_path);

            /* Upload to s3 */
            \Storage::put(config('attendize.event_images_path').'/'.$filename, file_get_contents($file_full_path));

            $eventImage = EventImage::createNew();
            $eventImage->image_path = config('attendize.event_images_path').'/'.$filename;
            $eventImage->event_id = $event->id;
            $eventImage->save();
        }
```
markup
---
```html
      <div class="form-group">
          {!! Form::label('event_image', 'Event Image (Flyer or Graphic etc.)', array('class'=>'control-label ')) !!}
          {!! Form::styledFile('event_image') !!}

      </div>
```
