search form
---
```html
      <form id="search-form" class="navbar-form navbar-right" role="search" style="display:none">
        <div class="form-group">
          <input type="text" id="search" style="width: 240px;padding-top:0px;padding-bottom:0px" 
            class="form-control" placeholder="{{ trans('texts.search') . ': ' . trans('texts.search_hotkey')}}">
        </div>
      </form>
```

drop down menu
---
```blade
      <ul class="nav navbar-nav navbar-right navbar-search"> 
        <li class="dropdown">
          <a href="#" onclick="showSearch()">
            <span class="glyphicon glyphicon-search" title="{{ trans('texts.search') }}"/>
          </a>
          <ul class="dropdown-menu">	        		        	
            @if (count(Session::get(RECENTLY_VIEWED)) == 0)
                <li><a href="#">{{ trans('texts.no_items') }}</a></li>
            @else
                @foreach (Session::get(RECENTLY_VIEWED) as $link)
                    @if (property_exists($link, 'accountId') && $link->accountId == Auth::user()->account_id)
                        <li><a href="{{ $link->url }}">{{ $link->name }}</a></li>	
                    @endif
                @endforeach
            @endif
          </ul>
        </li>
      </ul>
```
