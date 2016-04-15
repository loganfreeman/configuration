You write the template
---
```html
            <script type="text/template" id="template-calendar">
              <div class="cal_year"><%= year %></div>
            <div class="calender_sec">
                <div class="cal_detail_2">
                  <div class="cal_next clndr-previous-button"><img src="{{asset('assets/images/dashboard/cal_l.png')}}" alt=""></div>
                <div class="cal_month_2"><p><%= month %></p></div>
                <div class="cal_prav clndr-next-button"><img src="{{asset('assets/images/dashboard/cal_r.png')}}" alt=""></div>
              </div>
              <div class="cal_detail_3">
                  <table width="100%" border="0" cellpadding="0" cellspacing="0">
                  <tr>
                    <% _.each(daysOfTheWeek, function(day) { %>
                    <td ><%= day %></td>
                    <% }); %>
                  </tr>
                  <% for(var i = 0; i < numberOfRows; i++){ %>
                  <tr>
                    <% for(var j = 0; j < 7; j++){ %>
                    <% var d = j + i * 7; %>
                    <td class='<%= days[d].classes %>'><div class='day-contents'><%= days[d].day %>
                    </div></td>
                    <% } %>
                  </tr>
                  <% } %>
                </table>
              </div>
            </div>
            </script>
```
Javascript
---
```js
$('.cal2').clndr({
    template: $('#template-calendar').html(),
    daysOfTheWeek: ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
    events: {{$eventDates}},
    clickEvents: {
      click: function(e) {
        // console.log($(e.element).hasClass("event"));
        if ($(e.element).hasClass("event")) 
        {
          var tempclass = $(e.element).attr("class");
          var finaldate = tempclass.split('day-')[1];
          var eventsModel = new EventsList([], {
            selectedDate: finaldate
          });
          var eventsView = new EventsListView({
            collection: eventsModel
          });
          eventsView.render();

        } else 
        {

          //User has clicked a day in which no event is there
          // Hence do nothing
        }
      }
    }
  });
```
The `days` array contains most of the stuff we need to make a calendar. Its structure looks like this:
```js
{
    day: 5,
    events: [],
    classes: "day",
    date: moment("2015-12-31")
}
```
CLNDR accepts events as an array of objects:
```js
events = [
    {
        date: "YYYY-MM-DD or some other ISO Date format",
        and: "anything else"
    }
]
```
Php
```php
public function getEventDates($userId)
{
	
	$eventsId = \EventUser::where('user_id',$userId)->lists('events_id');
	if(sizeof($eventsId) !=0)
	{
	$eventDates =  \Events::whereIn('id',$eventsId)->get(array('date'))->toJson();
	return $eventDates;	
	}
	else
	{
		return json_encode([]);
	}
	
}
```

Add event through bootstrap modal
---
```html
<a data-toggle="modal" href="#myModal" class="add_project add-last"> + {{trans('92five.addEvent')}}</a>

<!-- Add Event Popup -->
<div id="myModal" class="modal hide fade cal_light_box" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <form class="form-horizontal" action='calendar/add' method='post' id="addevent" data-validate="parsley">
    <div class="modal-header form_modal_header">
      <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
      <h3 id="myModalLabel">
      <input type="text" name="title" id="title" class="popup_title_input" placeholder="Title" data-required="true"  data-show-errors="false" >
      </h3>
    </div>
    <div class="modal-body">
      <div class="popup_event">
        <div class="add-proj-form">
          <fieldset>
            <div class="row-fluid">
              <div class="control-group">
                <div class="row-fluid">
                  <input id="date" name="date" type="text" class="span6 pull-left" placeholder="When" data-required="true"  data-show-errors="true">
                  <input id="starttime" name="starttime" type="text" class="span3 pull-left" placeholder="From" data-required="true"  data-show-errors="true">
                  <input id="endtime" name="endtime" type="text" class="span3 pull-left" placeholder="Till" data-required="true"  data-show-errors="true">
                </div>
              </div>
              <div class="control-group">
                <label class="control-label" for="passwordinput">{{trans('92five.category')}}:</label>
                <div class="controls">
                  <div class="task_select">
                    <select name="category" id="category" tabindex="1" style="width:270px;" data-required="true"  data-show-errors="false">
                      <option name="" value="" selected="selected" title="">{{trans('92five.selectCategory')}}</option>
                      <option  name="" value="Meeting - General" title="">{{trans('92five.eventsCategory1')}}</option>
                      <option  name="" value="Meeting - Project" title="">{{trans('92five.eventsCategory2')}}</option>
                      <option  name="" value="Meeting - Task" title="">{{trans('92five.eventsCategory3')}}</option>
                      <option  name="" value="Deliverer" title="">{{trans('92five.eventsCategory4')}}</option>
                      <option  name="" value="Client" title="">{{trans('92five.client')}}</option>
                      <option  name="" value="Others" title="">{{trans('92five.others')}}</option>
                    </select>
                  </div>
                </div>
              </div>
              <div class="control-group">
                <label class="control-label" for="passwordinput">{{trans('92five.note')}}:</label>
                <div class="controls">
                  <textarea  name="note" id="note" class="add-proj-form-t" placeholder="Note"></textarea>
                </div>
              </div>
              <div class="control-group">
                <label class="control-label" for="passwordinput">{{trans('92five.people')}}:<span class="tooltipster-icon" title="To add the people start typing the name and select the appropriate user from the list. Please note that only those name will appear in list who are registered in the app. Please add your name as well if you are one of them.">(?)</span></label>
                <div class="controls">
                  <input id="plugin" name="passwordinput" type="text" placeholder="Add Name">
                </div>
                <div id="selected">
                  <ul id="list">
                  </ul>
                  <input style="display: none;" name="tagsinput" id="tagsinput" class="tagsinput" placeholder="Add Name" value=""/>
                  <p></p>
                </div>
              </div>
              <div class="advanced_link"><a href="#" id="adv">{{trans('92five.advanced')}}</a></div>
            </div>
            <div id="advanced-inputs">
              <div class="row-fluid event_form_data">
                <div class="control-group">
                  <label class="control-label" for="passwordinput">{{trans('92five.location')}}:</label>
                  <div class="controls">
                    <input id="location" name="location" type="text" placeholder="Location">
                  </div>
                </div>
              </div>
            </div>
          <button class="submit pull-right">{{trans('92five.submit')}}</a></button>
        </fieldset>
      </div>
    </div>
  </div>
</form>
</div>
<!-- End Add Event Popup -->
<!-- Delete Event Popup -->
<div id="myModal-item-delete" class="modal cal_light_box hide fade" tabindex="-1" role="dialog" aria-labelledby="myModalLabel" aria-hidden="true">
  <div class="modal-header">
    <button type="button" class="close" data-dismiss="modal" aria-hidden="true">×</button>
    <h3 id="myModalLabel">{{trans('92five.really')}} ?</h3>
  </div>
  <div class="modal-body">
    <div class="confirm-delete">{{trans('92five.confirmDeleteEvent')}}?</div>
    <div class="confirm-button">
      <form method="post" action="calendar/event/delete">  <input type="hidden" name="deleteEventId" id="deleteEventId" value=  > <button class="submit">{{trans('92five.yesPlease')}}.</a></button></form>
    <button class="submit dontdelete" id="dontdelete" >{{trans('92five.noThanks')}}.</a></button></div>
  </div>
</div>
<!-- End Delete Event Popup-->
```
