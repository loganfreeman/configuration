Sass's @content Directive
---
1. Media Queries
```sass
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
```sass
@media only screen and (max-width: 320px) {
  background: red;
}
```
