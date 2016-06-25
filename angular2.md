byte format pipe
---
```js
@Pipe({ name: 'byteFormat'})
class ByteFormatPipe implements PipeTransform {
  // Credit: http://stackoverflow.com/a/18650828
  transform(bytes, args) {
    if(bytes == 0) return '0 Bytes';
    var k = 1000;
    var sizes = ['Bytes', 'KB', 'MB', 'GB'];
    var i = Math.floor(Math.log(bytes) / Math.log(k));
    return (bytes / Math.pow(k, i)).toFixed(1) + ' ' + sizes[i];
  }
}
```
jQuery
---
```js
import {Component, ElementRef, OnInit} from '@angular/core';
declare var jQuery:any;
@Component({
    selector: 'jquery-integration',
    templateUrl: './components/jquery-integration/jquery-integration.html'
})
export class JqueryIntegration implements OnInit {
    elementRef: ElementRef;
    constructor(elementRef: ElementRef) {
        this.elementRef = elementRef;
    }
    ngOnInit() {
        jQuery(this.elementRef.nativeElement).draggable({containment:'#draggable-parent'});
    }
}
```
