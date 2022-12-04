---
title: Drawing with Mouse and Touch Events on HTML
date: 2022-12-04
categories:
- louissrliu
- miscellaneous
tags:
- html
- javascript
toc: true
language: zh-tw/en
thumbnailImagePosition: left
thumbnailImage: https://d3p3tllh95j94n.cloudfront.net/wellcome-to-louissrliu/dock.jpeg
---

The HTML canvas can be used for sketching/drawing either by mouse or by touch. In the [previous post][mouse], I quickly discuss how to use Canvas API to implement a canvas with mouse sketching. In this post, I would like to describe how go about implementing for sketching/drawing by mouse and touch.

<!-- more -->

## Mouse and Touch Canvas ##

The belowing canvas can be used with any devices that use mouse or touch screen:

<div style="overflow-x:auto;">
    <iframe src="/javascript/canvas/mouse_touch_canvas.html" style="width:100%; height:360px", title="add-default-marker"></iframe>
</div>

## Source Code ##

The source code was referring to [Mozilla touch-based drawing][touchbased] and was slightly modified with some feature enhancement.

{% codeblock %}
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
<canvas id="canvas" width="700" height="360"></canvas>
<button onclick="javascript:clearArea();return false;">Clear Area</button>

Line width : <select id="selWidth">
    <option value="5" selected="selected">5</option>
    <option value="7">7</option>
    <option value="9">9</option>
    <option value="11">11</option>
    <option value="13">13</option>
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
var offsetX;
var offsetY;

const canvas = document.getElementById('canvas');
const context = canvas.getContext('2d');

function startup() {
  canvas.addEventListener('touchstart', handleStart);
  canvas.addEventListener('touchend', handleEnd);
  canvas.addEventListener('touchcancel', handleCancel);
  canvas.addEventListener('touchmove', handleMove);
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
    if(isDrawing) {
      drawLine(context, x, y, e.offsetX, e.offsetY);
      x = 0;
      y = 0;
      isDrawing = false;
    }
  });
}

document.addEventListener("DOMContentLoaded", startup);

const ongoingTouches = [];

function handleStart(evt) {
  evt.preventDefault();
  const touches = evt.changedTouches;
  offsetX = canvas.getBoundingClientRect().left;
  offsetY = canvas.getBoundingClientRect().top;
  for (let i = 0; i < touches.length; i++) {
    ongoingTouches.push(copyTouch(touches[i]));
  }
}

function handleMove(evt) {
  evt.preventDefault();
  const touches = evt.changedTouches;
  for (let i = 0; i < touches.length; i++) {
    const color = document.getElementById('selColor').value;
    const idx = ongoingTouchIndexById(touches[i].identifier);
    if (idx >= 0) {
      context.beginPath();
      context.moveTo(ongoingTouches[idx].clientX - offsetX, ongoingTouches[idx].clientY - offsetY);
      context.lineTo(touches[i].clientX - offsetX, touches[i].clientY - offsetY);
      context.lineWidth = document.getElementById('selWidth').value;
      context.strokeStyle = color;
      context.lineJoin = "round";
      context.closePath();
      context.stroke();
      ongoingTouches.splice(idx, 1, copyTouch(touches[i])); // swap in the new touch record
    }
  }
}

function handleEnd(evt) {
  evt.preventDefault();
  const touches = evt.changedTouches;
  for (let i = 0; i < touches.length; i++) {
    const color = document.getElementById('selColor').value;
    let idx = ongoingTouchIndexById(touches[i].identifier);

    if (idx >= 0) {
      context.lineWidth = document.getElementById('selWidth').value;
      context.fillStyle = color;
      ongoingTouches.splice(idx, 1); // remove it; we're done
    }
  }
}

function handleCancel(evt) {
  evt.preventDefault();
  const touches = evt.changedTouches;
  for (let i = 0; i < touches.length; i++) {
    let idx = ongoingTouchIndexById(touches[i].identifier);
    ongoingTouches.splice(idx, 1); // remove it; we're done
  }
}

function copyTouch({ identifier, clientX, clientY }) {
  return { identifier, clientX, clientY };
}

function ongoingTouchIndexById(idToFind) {
  for (let i = 0; i < ongoingTouches.length; i++) {
    const id = ongoingTouches[i].identifier;
    if (id === idToFind) {
      return i;
    }
  }
  return -1; // not found
}

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

Note:
Touch events are similar to mouse events except they support simultaneous touches and at different locations on the touch surface. This example was extended the functionality of mouse events with touch events, which uses ongoingTouches array for the track of the touches in-progress. When a touchstrat event occurs, indicating that a new touch on the surface has occurred. After drawing the line, we call the array.splice() to replace the previous information about the touchpoint with the current information into ongoingTouches array.

## Refereence ##

- [Github: Touch events API](https://github.com/mdn/content/blob/main/files/en-us/web/api/touch_events/index.md?plain=1)

- [Mozilla Touch events](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events)

[touchbased]:https://developer.mozilla.org/en-US/docs/Web/API/Touch_events "https://developer.mozilla.org/en-US/docs/Web/API/Touch_events"

[mouse]:https://louissrliu.github.io/2022/11/06/Drawing_with_mouse_events_on_html/ "https://louissrliu.github.io/2022/11/06/Drawing_with_mouse_events_on_html/"

<p>Thanks for reading! Feel free to leave the comments below or <a href="mailto:qazqazqaz850@gmail.com">email</a> to me. Any pieces of advice or discussions are always welcome. :)</p>
