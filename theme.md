theme switch
---
```coffee
class ThemeSwitcherView
  constructor: ->
    @body = $('body')
    $(document).on('change', '.theme-switch', @switchTheme)
    @loadTheme()

  switchTheme: (e) =>
    localStorage.setItem('browserlog-theme', e.target.value)
    @loadTheme()

  loadTheme: ->
    $('.theme-switch').val(@getCurrentTheme())
    @body.attr('data-theme', @getCurrentTheme())

  getCurrentTheme: ->
    localStorage.getItem('browserlog-theme') || 'dark'
```
