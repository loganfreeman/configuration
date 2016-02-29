```coffee
  updateScroll: ->
    @body.scrollTop(@body.prop('scrollHeight'))
    @timestamp = (new Date()).getTime()
```
