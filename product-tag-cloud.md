markdown
---
```php
  <div class="panel panel-default">
      <div class="panel-heading">
          {{ trans('globals.popular_tags') }}
      </div>
      <div class="panel-body">
          <div class="tags-cloud">
              @foreach ($tagsCloud as $tag)
                  <a href="{{ action('ProductsController@index') }}/?search={{ $tag }}" class="t{{ mt_rand(1,10) }}" >
                      {{ $tag }}
                  </a>
              @endforeach
          </div>
      </div>
  </div>
```
styles
---
```css
.tags-cloud
{
	font-size: 19px;
	text-align: center;
	padding: 10px;

	a {
		text-decoration: none;
		margin: 0 5px 5px 0;
	}

	a.t10 {
		color: @pomegranate;
	    font-size: 115%;
	    font-weight: bold;
	}

	a.t10:hover {
		background-color: @pomegranate;
		color: @ligh;
	}

	a.t9 {
		color: @pomegranate;
		font-size: 110%;
	}

	a.t9:hover {
		background-color: @pomegranate;
		color: @ligh;
	}

	a.t8 {
		color: @orange;
	    font-size: 105%;
	    font-weight: bold;
	}

	a.t8:hover {
		background-color: @orange;
		color: @ligh;
	}

	a.t7 {
		color: @orange;
		font-size: 100%;
	}

	a.t7:hover {
		background-color: @orange;
		color: @ligh;
	}

	a.t6 {
		color: @nephritis;
	    font-size: 95%;
	    font-weight: bold;
	}

	a.t6:hover {
		background-color: @nephritis;
		color: @ligh;
	}

	a.t5 {
		color: @emerald;
		font-size: 90%;
	}

	a.t5:hover {
		background-color: @emerald;
		color: @ligh;
	}

	a.t4 {
		color: @emerald;
		font-size: 80%;
	    font-weight: bold;
	}

	a.t4:hover {
		background-color: @emerald;
		color: @ligh;
	}

	a.t3 {
		color: @turquoise;
		font-size: 75%;
	}

	a.t3:hover {
		background-color: @turquoise;
		color: @ligh;
	}

	a.t2 {
		color: @peter-river;
		font-size: 65%;
	    font-weight: bold;
	}

	a.t2:hover
	{
		background-color: @peter-river;
		color: @ligh;
	}

	a.t1 {
		color: @concrete;;
		font-size: 60%;
	}

	a.t1:hover {
		background-color: @concrete;;
		color: @ligh;
	}
}// tags cloud end
```
