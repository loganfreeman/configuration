```ruby
# app/assets/images/logo.png
= image_tag('logo')

# Outputs something like <img src="/assets/logo-[hash].png" />

# public/images/logo.png
= image_tag('/images/logo.png')

# Outputs something like
# <img src="/images/logo.png" />
```
