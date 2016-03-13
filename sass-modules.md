button
---
```sass
%button {
	display: inline-block;
	padding: 0.3rem 1.5rem;
    font-weight: 300;
    -webkit-font-smoothing: auto;
    cursor: pointer;
    vertical-align: middle;

    &:active {
        margin: 1px 0 -1px 0;
    }

    i {
        margin-right: 5px;
    }

    &.button-small {
        padding: 2px 10px;
        font-size: 1rem;
    }

    &.button-x-small {
        padding: 2px 8px 2px 5px;
        font-size: 0.9rem;
    }

}

@mixin button-color($color, $text:$white, $lighter:null) {
	color: rgba($text, 0.85);
	border-radius: $border-radius;
    background: $color;

    @if ($lighter == null) {
        $lighter: lightness($color) > 50;
    }

    @if ($lighter) {
        &:hover {
            background: shade($color,15%);
            color: $text;
        }
        &.dropdown-toggle {
            border-left: 1px solid lighten($color, 5%);
        }
    } @else {
        &:hover {
            background: tint($color,15%);
            color: $text;
        }
        &.dropdown-toggle {
            border-left: 1px solid darken($color, 5%);
        }
    }
}
```
collapse and collapsing
---
```sass
.collapse {
    display: none;
    &.in {
        display: block;
    }

    tr &.in {
        display: table-row;
    }

    tbody &.in {
        display: table-row-group;
    }
}

.collapsing {
    position: relative;
    height: 0;
    overflow: hidden;
    @include transition(height .35s ease, visibility .35s ease);
}
```
list unstyled
---
```css
    .list-unstyled {
        padding-left: 0;
        list-style: none;
    }
```
dropdown menu
---
```css
.dropdown-menu {
        position: absolute;
        z-index: 1000;
        display: none;
        float: left;
        min-width: 160px;
        background-color: #fff;
        -webkit-background-clip: padding-box;
        background-clip: padding-box;
        border: 1px solid rgba(0, 0, 0, .15);
        border-radius: 4px;
        box-shadow: 0 6px 12px rgba(0, 0, 0, .175);
    }
```
