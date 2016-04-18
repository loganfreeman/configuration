```sass
    .steps {
        @extend .row;
        margin: 0 auto;
        border-radius: 2px 2px 0 0;
        margin-bottom: 20px;
        .step {
            @extend .col-xs-3;
            padding: 20px 0;
            text-align: center;
            position: relative;
            font-size: 13px;
            &:not(:last-child):after {
                content: '';
                position: absolute;
                bottom: 31px;
                left: 55%;
                display: block;
                height: 1px;
                background: #94A1B8;
                width: 100%;
            }
            span {
                width: 23px;
                height: 23px;
                display: block;
                position: relative;
                margin: 0 auto;
                margin-top: 13px;
                border-radius: 25px;
                background: $cachet-base-medium;
                border: 1px solid #94A1B8;
                -webkit-transition: all 0.2s linear;
                -moz-transition: all 0.2s linear;
                -ms-transition: all 0.2s linear;
                -o-transition: all 0.2s linear;
                transition: all 0.2s linear;
            }

            &.active {
                span {
                    background: $cachet-primary;
                }
            }
        }
```
javascript
---
```js
    function goToStep(current, next) {
        // validation was ok. We can go on next step.
        $('.block-' + current)
          .removeClass('show')
          .addClass('hidden');

        $('.block-' + next)
          .removeClass('hidden')
          .addClass('show');

        $('.steps .step')
            .removeClass("active")
            .filter(":lt(" + (next) + ")")
            .addClass("active");
    }
```
