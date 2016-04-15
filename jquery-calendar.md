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
