---
layout: page
title: About
date: 2016-02-10 08:31:00
permalink: /about/
---

<!--
<div style="background-image:url(/images/background_4.png);
width:100%;height:100%;color:white;font-size:18px;">
-->

##Hello!  
  
I am Sagar Gandhi.
<h1 style = "text-align: center;" markdown = "1">I</h1>  
  
  
<canvas id="I_AM" width="0px" height="0px">
Your browser does not support the HTML5 canvas tag.
</canvas>

<script>
(function() {
var
// Obtain a reference to the canvas element
// using its id.
htmlCanvas = document.getElementById('I_AM'),
   // Obtain a graphics context on the
   // canvas element for drawing.
   ctx = htmlCanvas.getContext('2d');

// Start listening to resize events and
// draw canvas.
initialize();

function initialize() 
{
  // Register an event listener to
  // call the resizeCanvas() function each time
  // the window is resized.
  window.addEventListener('resize', resizeCanvas, false);
  // Draw canvas border for the first time.
  resizeCanvas();
}

// Display custom canvas.
// In this case it's a blue, 5 pixel border that
// resizes along with the browser window.
function redraw(width, height)
{
  var start = width/2;
  var margin = width * 0.01;
  var height = (width / 3) - (margin * 4);
  var end_step = width/4
  ctx.font = "16px Open Sans";
  var gradient = ctx.createLinearGradient(0, 0, width, 0);
  gradient.addColorStop("0",    "#0000FF");
  gradient.addColorStop("0.5",  "#FFFFDD");
  gradient.addColorStop("1.0",  "#DFDFDF");

  ctx.beginPath();
  
  var punch_1 = "LEARN"
  ctx.moveTo(start - margin, 0);
  ctx.bezierCurveTo((start - height/3), 0 , (start - height/3), height , end_step*1, height);
  ctx.fillText(punch_1, (end_step*1) - (width*0.05), (height+3*margin))

  var punch_5 = "MENTOR"
  ctx.moveTo(start + margin, 0);
  ctx.bezierCurveTo((start + height/3), 0 , (start + height/3), height , end_step*3, height);
  ctx.fillText(punch_5, (end_step*3) - (width*0.05), (height+3*margin))

  var punch_2 = "RESEARCH"
  ctx.moveTo(start - margin, 0);
  ctx.bezierCurveTo((start - height*0.33), 0 , (start - height*0.16), height , end_step*1.5, height);
  ctx.fillText(punch_2, (end_step*1.5) - (width*0.05), (height+3*margin))

  var punch_4 = "TEACH"
  ctx.moveTo(start + margin, 0);
  ctx.bezierCurveTo((start + height*0.33), 0 , (start + height*0.16), height , end_step*2.5, height);
  ctx.fillText(punch_4, (end_step*2.5) - (width*0.05), (height+3*margin))

  var punch_5 = "CODE"
  ctx.moveTo(start, 0);
  ctx.bezierCurveTo((start ), 0 , (start ), height , end_step*2, height);
  ctx.fillText(punch_5, (end_step*2) - (width*0.025), (height+3*margin))


  ctx.stroke();
}

// Runs each time the DOM window resize event fires.
// Resets the canvas dimensions to match window,
// then draws the new borders accordingly.
function resizeCanvas()
{
  var width = 0;
  var height = 0;

  var contentElement = document.getElementsByClassName("content-box post")[0]
  width = htmlCanvas.width = (contentElement.clientWidth * 0.85)
  height = htmlCanvas.height = (contentElement.offsetWidth*0.85 * 0.33);

  redraw(width, height);
}

})();
</script>

<br>

Currently I am based in Pune, India.  
I work at [Persistent LABS](http://www.persistent.com/) as Senior Software Engineer. I am mostly involved in [Computer Vision](https://en.wikipedia.org/wiki/Computer_vision) and [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) work, solving real world problems in real time.
  
##Past  
  
I have worked on Computer Graphics. You can find references some cool demos on projects page. I have also consulted multiple start ups in articulating development pipeline.  
I also hold a MS degree in Scientific Computing from [Interdesciplinary School of Scientific Computing](http://issc.unipune.ac.in/).  
  
I am interested in working with smart people, using cutting edge technology, and solving real life problems. So feel free to get in touch at gandhi.sagar.gs@gmail.com  
  




