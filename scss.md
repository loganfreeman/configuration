Sass's @content Directive
---
1. Media Queries
```scss
@mixin media($width) {
  @media only screen and (max-width: $width) {
    @content;
  }
}

@include media(320px) {
  background: red;
}
```
generates:
```scss
@media only screen and (max-width: 320px) {
  background: red;
}
```
