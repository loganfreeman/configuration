javascript
```js
// by - coursesweb.net

var ldsec = 2.75;           // define the number of secconds till the progress bar reaches 100%
var ldpercent = 4;         // percentage for loading progress bar
var ldspeed = ldsec * 1000 / (100/ldpercent);         // set the speed for progress bar

// data to be displayed in loading bar, when reaches 100%
// a tag that close the block with the content with the loading bar
//var ldtxt = '<u style="cursor:pointer;" onclick="document.getElementById(\'loading\').style.display=\'none\'">Close</u>';      

// define a recursive function to set and add the progress bar
function simLoad(div) {
  // get the object with the ID from "div", the tag which represents the progress bar
  var dvload = document.getElementById(div);
  ldpercent += 5;           // increments the percentage
  if(ldpercent>100) ldpercent = 100;       // ensure that percentages do not exceed 100

  // change the width of the progress bar, and display the percentage
  dvload.style.width = ldpercent+ '%';
  

  // if the percentage is less than 100, auto-calls this function
  // else, calls the function finLoad()
  if(ldpercent<100) setTimeout("simLoad('"+div+"')", ldspeed);
  else finLoad(dvload);
}

// function that can be executed when the progress bar is completed
// receives the object which contains the tag with the loading bar
function finLoad(divobj) {
  /** Here you can add JavaScript instructions to be executed when the progress bar reaches 100% **/
  window.location.href='{{URL::to('dashboard');}}';

  // adds a close button in the loading bar, to close that block
  
}
  simLoad('loadbar');     // to display the loading bar once the page is loaded
```
markup
```
<div class="loading_main">
    <div id="loading">
    	<div class="loading_detail">{{trans('92five.authenticatingTitle')}}....</div>
    	<div id="loading_inner">
        	<div id="loadbar">&nbsp;</div>
        </div>
    </div>
</div>
```
css
```
.loading_main {
    width: 420px;
    margin: 0 auto;
    padding: 0;
}
#loading {
    width: 420px;
    float: left;
    margin: 280px 0 0 0;
    padding: 0;
    position: relative;
}
#loading_inner {
    width: 420px;
    float: left;
    margin: 0;
    padding: 0;
    text-align: center;
    position: absolute;
    top: 48%;
    color: #fff;
    /*left:40%;*/

background: #22252b;
}
#loading #loadbar {
    width: 0%;
    height: 10px;
    /*background-color:blue;*/

background: url(../../images/authenticate/loader_bg.jpg) no-repeat left top;
    background-size: 100% 10px;
}
.loading_detail {
    width: 100%;
    float: left;
    padding: 0;
    margin: 0 0 80px 0;
    text-align: center;
    font-size: 30px;
    color: #000000;
    font-family: 'Lato-reg';
}
```
