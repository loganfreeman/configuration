Sass's @content Directive
---
- Media Queries
```sass
@mixin media($width) {
  @media only screen and (max-width: $width) {
    @content;
  }
}

@include media(320px) {
  background: red;
}

// generates
@media only screen and (max-width: 320px) {
  background: red;
}
```

breakpoints
```sass
// Breakpoints
@mixin breakpoint($breakpoints, $media: all) {
    @each $breakpoint in $breakpoints {
        @if $breakpoint == large-desktop-range {
            @media only #{$media} and (min-width: $large-desktop-container) { @content; }
        }
        @else if $breakpoint == desktop-range {
            @media only #{$media} and (min-width: $desktop-container) and (max-width: $large-desktop-container - 0.062) { @content; }
        }
        @else if $breakpoint == tablet-range {
            @media only #{$media} and (min-width: $tablet-container) and (max-width: $desktop-container - 0.062) { @content; }
        }
        @else if $breakpoint == large-mobile-range {
            @media only #{$media} and (min-width: $large-mobile-container + 0.063) and (max-width: $tablet-container - 0.062) { @content; }
        }
        @else if $breakpoint == small-mobile-range {
            @media only #{$media} and (max-width: $large-mobile-container) { @content; }
        }
        @else if $breakpoint == no-mobile {
            @media only #{$media} and (min-width: $tablet-container) { @content; }
        }
        @else if $breakpoint == mobile-only {
            @media only #{$media} and (max-width: $tablet-container - 0.062) { @content; }
        }
        @else if $breakpoint == desktop-only {
            @media only #{$media} and (max-width: $desktop-container - 0.062) { @content; }
        }
    }

}
```
* keyframes
```sass
@mixin keyframes($name) {
  @-webkit-keyframes #{$name} {
    @content;
  }

  @-moz-keyframes #{$name} {
    @content;
  }

  @keyframes #{$name} {
    @content;
  }
}

@include keyframes(fadeIn) {
  from {
    opacity: 0%;
  }
  to {
    opacity: 100%;
  }
}
```
* context specificity
```sass
@mixin create-context($classes...) {
  @each $class in $classes {
    .#{$class} & {
      @content;
  }
}

@mixin context--alternate-template {
  @include create-context(about, blog) {
    @content
  }
}

.header {
  height: 12em;
  background: red;

  @include context--alternate-template {
    background: green;
  }
}
```
vertical centering
---
```sass
// Vertical Centering
%vertical-align {
	position: relative;
	top: 50%;
	-webkit-transform: translateY(-50%);
	-moz-transform: translateY(-50%);
	-o-transform: translateY(-50%);
	-ms-transform: translateY(-50%);
	transform: translateY(-50%);
}
```
