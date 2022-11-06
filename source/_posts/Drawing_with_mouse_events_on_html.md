---
title: Drawing with Mouse Events on HTML
date: 2022-11-06
categories:
- louissrliu
- miscellaneous
tags:
- html
- javascript
toc: true
language: en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/castle.jpeg
---

The HTML canvas can be used for sketching/drawing by mouse. The canvas API provides a means for drawing graphics via JavaScript and the HTML element. It largely focuses on 2D graphics. In this post, I would like to describe how to quickly go about implementing this.

<!-- more -->

## Mouse Canvas ##

The mouse canvas can be used with any devices (laptop/mobile..) that use mouse. Belowing is the simple example:

<div style="overflow-x:auto;">
    <iframe src="/javascript/canvas/mouse_canvas.html" style="width:100%; height:360px", title="add-default-marker"></iframe>
</div>

## Source Code ##

The source code was referring to [Mozilla mouse drawing mousemove][mouse] with clear/line width/color features enhancement.

{% codeblock %}
// CSS Style
<style>
#canvas_div {
    text-align: center;
    display: block;
    margin-left: auto;
    margin-right: auto;
}
canvas {
  border: 2px solid black;
}
</style>

<div id="canvas_div" style="overflow-x: auto;">
<canvas id="canvas" width="900" height="360"></canvas>

<button onclick="javascript:clearArea();return false;">Clear Area</button>

Line width : <select id="selWidth">
    <option value="11">11</option>
    <option value="13" selected="selected">13</option>
    <option value="15">15</option>
</select>

Color : <select id="selColor">
    <option value="black">black</option>
    <option value="blue" selected="selected">blue</option>
    <option value="red">red</option>
    <option value="green">green</option>
    <option value="yellow">yellow</option>
    <option value="gray">gray</option>
</select>
</div>

<script>
// When true, moving the mouse draws on the canvas
let isDrawing = false;
let x = 0;
let y = 0;

const canvas = document.getElementById('canvas');
const context = canvas.getContext('2d');

// event.offsetX, event.offsetY gives the (x,y) offset from the edge of the canvas.

// Add the event listeners for mousedown, mousemove, and mouseup
canvas.addEventListener('mousedown', (e) => {
  x = e.offsetX;
  y = e.offsetY;
  isDrawing = true;
});

canvas.addEventListener('mousemove', (e) => {
  if (isDrawing) {
    drawLine(context, x, y, e.offsetX, e.offsetY);
    x = e.offsetX;
    y = e.offsetY;
  }
});

canvas.addEventListener('mouseup', (e) => {
  if (isDrawing) {
    drawLine(context, x, y, e.offsetX, e.offsetY);
    x = 0;
    y = 0;
    isDrawing = false;
  }
});

function drawLine(context, x1, y1, x2, y2) {
  context.beginPath();
  context.strokeStyle = document.getElementById('selColor').value;
  context.lineWidth = document.getElementById('selWidth').value;
  context.lineJoin = "round";
  context.moveTo(x1, y1);
  context.lineTo(x2, y2);
  context.closePath();
  context.stroke();
}

function clearArea() {
    context.setTransform(1, 0, 0, 1, 0, 0);
    context.clearRect(0, 0, context.canvas.width, context.canvas.height);
}
</script>
{% endcodeblock %}

## Reference ##

+ [Mozilla: Element: mousemove event](https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event)

[mouse]:https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event "https://developer.mozilla.org/en-US/docs/Web/API/Element/mousemove_event"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
