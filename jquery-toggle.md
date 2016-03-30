slideToggle
---
`.slideToggle( [duration ] [, complete ] )`

*Description* : Display or hide the matched elements with a sliding motion

toggle and slideToggle has no difference in usage unless slideToggle has a sliding motion associated with it.
```coffee

    EXPANDED: "&#9660;"
    COLLAPSED: "&#9658;"

    #----------------------------------------------------------------------------
    flip_subtitle: (el) ->
      $el = $(el)
      arrow = $el.find("small")
      intro = $el.parent().next().children("small")

      # Optionally, the intro might be next to the link.
      intro = $el.next("small")  unless intro.length
      section = $el.parent().next().children("div")
      section.slideToggle(
        250
        =>
          arrow.html(if section.css('display') is 'none' then @COLLAPSED else @EXPANDED)
          intro.toggle()
      )
```

Here is the definition of subtitle
```ruby
  #----------------------------------------------------------------------------
  def subtitle(id, hidden = true, text = id.to_s.split("_").last.capitalize)
    content_tag("div",
                link_to("<small>#{ hidden ? '&#9658;' : '&#9660;' }</small> #{sanitize text}".html_safe,
                        url_for(controller: :home, action: :toggle, id: id),
                        remote: true,
                        onclick: "crm.flip_subtitle(this)"
                ), class: "subtitle")
  end
```
when the subtitle is clicked, it finds the arrow. The section following the div containing the subtitle will slideToggle. after the following section slideToggle, the small introduction inside the following section will also toggle.


fadeIn and fadeOut
---
```coffee
      flick: function(id, action) {
        var $el;
        $el = $("#" + id);
        if ($el.length) {
          switch (action) {
            case "show":
              return $el.show();
            case "hide":
              return $el.hide();
            case "clear":
              return $el.html("");
            case "remove":
              return $el.remove();
            case "toggle":
              return $el.toggle();
          }
        }
      },
      flash: function(type, sticky) {
        var $el;
        $el = $("#flash");
        $el.hide();
        if (type === "warning" || type === "error") {
          $el.addClass("flash_warning");
        } else {
          $el.addClass("flash_notice");
        }
        $el.fadeIn(500);
        if (!sticky) {
          return setTimeout((function() {
            return $el.fadeOut(500);
          }), 3000);
        }
      }
```
