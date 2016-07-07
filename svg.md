Animating Vectors with SVG
---
html
```html
<button onclick="rerun();">Again!</button>

<div id="myobj" width="100%" height="100%">
<svg version="1.1" id="Layer_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
     viewBox="0 0 152.5 63.5" enable-background="new 0 0 152.5 63.5" xml:space="preserve">
<path id="i0" fill="none" stroke="#000000" d="M22.057,5.69c2.4,0,4.596,0.354,6.588,1.062s3.696,1.71,5.112,3.006
    c1.416,1.296,2.52,2.856,3.312,4.68c0.792,1.824,1.188,3.852,1.188,6.084c0,1.92-0.282,3.696-0.846,5.328
    c-0.564,1.632-1.32,3.192-2.268,4.68c-0.948,1.488-2.058,2.928-3.33,4.32c-1.272,1.393-2.604,2.808-3.996,4.248L16.081,51.086
    c1.152-0.336,2.28-0.594,3.384-0.774c1.104-0.18,2.16-0.27,3.168-0.27h13.356c0.959,0,1.722,0.27,2.286,0.81
    c0.563,0.54,0.846,1.254,0.846,2.142v5.328H3.409V55.37c0-0.6,0.126-1.224,0.378-1.872c0.252-0.648,0.654-1.248,1.206-1.8
    l15.804-15.84c1.319-1.344,2.502-2.628,3.546-3.852s1.92-2.436,2.628-3.636c0.708-1.2,1.248-2.417,1.62-3.654
    c0.372-1.236,0.558-2.538,0.558-3.906c0-1.248-0.18-2.345-0.54-3.294c-0.36-0.948-0.876-1.746-1.548-2.394
    c-0.672-0.648-1.47-1.134-2.394-1.458c-0.924-0.324-1.962-0.486-3.114-0.486c-2.136,0-3.894,0.54-5.274,1.62
    c-1.38,1.08-2.346,2.532-2.898,4.356c-0.265,0.912-0.661,1.566-1.188,1.962c-0.528,0.396-1.201,0.594-2.016,0.594
    c-0.36,0-0.756-0.036-1.188-0.108l-4.68-0.828c0.36-2.496,1.056-4.686,2.088-6.57c1.031-1.884,2.322-3.456,3.87-4.716
    c1.548-1.26,3.324-2.208,5.328-2.844C17.599,6.008,19.753,5.69,22.057,5.69z"/>
<path id="i1" fill="none" stroke="#000000" d="M72.205,38.702h6.552v5.148c0,0.48-0.156,0.895-0.468,1.242
    c-0.312,0.348-0.769,0.522-1.368,0.522h-4.716v12.708h-7.776V45.614h-21.96c-0.6,0-1.128-0.186-1.584-0.558
    c-0.456-0.372-0.744-0.833-0.864-1.386l-0.9-4.5l24.66-32.94h8.424V38.702z M64.429,21.674c0-0.768,0.023-1.596,0.072-2.484
    c0.048-0.888,0.132-1.812,0.252-2.772L48.589,38.702h15.84V21.674z"/>
<path id="i2" fill="none" stroke="#000000" d="M91.166,23.69v34.632H145.5V23.69h-20c0,0,20,0,19.952-8.701
    c-0.141-25.291-27.119,3.761-27.119,3.761S91.166-10,91.178,14.995c0.004,8.894,18.447,8.63,18.447,8.63L91.166,23.69z"/>
<path id="i3" fill="none" stroke="#000000" d="M114.125,22.5c0,0-15.75,10.625-16.25,19.25l5.875-3.882l-0.375,11.243
    c3.75-8.36,13.875-20.485,13.875-20.485s8.875,8.5,9.25,18l0.875-9.5L135.5,42.5c-4.125-11.016-14.75-20-14.75-20
    s11.983-3.356,15.75-4.625C140.582,16.5,145.393,11.72,139,9c-5.721-2.434-21.506,13.375-21.506,13.375S107.375,11,99.375,8.712
    c-6.565-1.877-5.954,7.663-0.25,9.663S114.125,22.5,114.125,22.5z"/>
</svg></div>

```
js
```js
var current_frame, total_frames, path, length, handle, myobj;

myobj = document.getElementById('myobj').cloneNode(true);

var init = function() {
  current_frame = 0;
  total_frames = 60;
  path = new Array();
  length = new Array();
  for(var i=0; i<4;i++){
    path[i] = document.getElementById('i'+i);
    l = path[i].getTotalLength();
    length[i] = l;
    path[i].style.strokeDasharray = l + ' ' + l; 
    path[i].style.strokeDashoffset = l;
  }
  handle = 0;
}
 
 
var draw = function() {
   var progress = current_frame/total_frames;
   if (progress > 1) {
     window.cancelAnimationFrame(handle);
   } else {
     current_frame++;
     for(var j=0; j<path.length;j++){
         path[j].style.strokeDashoffset = Math.floor(length[j] * (1 - progress));
     }
     handle = window.requestAnimationFrame(draw);
   }
};

init();
draw();

var rerun = function() {
  var old = document.getElementsByTagName('div')[0];
  old.parentNode.removeChild(old);
  document.getElementsByTagName('body')[0].appendChild(myobj);
  init();
  draw();
};
```
