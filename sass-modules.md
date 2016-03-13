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
```
