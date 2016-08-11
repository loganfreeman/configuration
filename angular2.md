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
ngDoCheck
---
```js
  @Input()
  set ngClass(v: string|string[]|Set<string>|{[key: string]: any}) {
    this._cleanupClasses(this._rawClass);

    if (isString(v)) {
      v = (<string>v).split(' ');
    }

    this._rawClass = <string[]|Set<string>>v;
    this._iterableDiffer = null;
    this._keyValueDiffer = null;
    if (isPresent(v)) {
      if (isListLikeIterable(v)) {
        this._iterableDiffer = this._iterableDiffers.find(v).create(null);
      } else {
        this._keyValueDiffer = this._keyValueDiffers.find(v).create(null);
      }
    }
  }
  
  ngDoCheck(): void {
    if (isPresent(this._iterableDiffer)) {
      var changes = this._iterableDiffer.diff(this._rawClass);
      if (isPresent(changes)) {
        this._applyIterableChanges(changes);
      }
    }
    if (isPresent(this._keyValueDiffer)) {
      var changes = this._keyValueDiffer.diff(this._rawClass);
      if (isPresent(changes)) {
        this._applyKeyValueChanges(changes);
      }
    }
  }
```
detached change detector
---
```js
 @Component({
   selector: 'giant-list',
   template: `
     <li ngFor="let d of dataProvider.data">Data {{d}}</lig>
   `,
   directives: [NgFor]
 })
 class GiantList {
   constructor(private ref: ChangeDetectorRef, private dataProvider:DataProvider) {
     ref.detach();
     setInterval(() => {
       this.ref.detectChanges();
     }, 5000);
   }
 }
```
