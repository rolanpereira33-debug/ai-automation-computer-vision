---
description: HTML5, JavaScript, Canvas API
icon: eye
---

# JS for CV: Browser Vision Basics

### Learning Objectives

* Explain the browser's permission model for webcam access (why a prompt appears)
* Request webcam access using navigator.mediaDevices.getUserMedia()
* Bind a MediaStream to a \<video> element and autoplay the live feed
* Create a \<canvas> element and obtain a 2D rendering context
* Draw a video frame onto a canvas using ctx.drawImage()
* Extract and display the RGBA pixel value at any (x, y) coordinate using ctx.getImageData()
* Overlay text on a canvas frame using ctx.fillText() and ctx.strokeText()
* Run a continuous animation loop using requestAnimationFrame() instead of setInterval()



### Why Browser CV Matters

Python is powerful but needs an installed environment. The browser needs nothing — no pip, no venv, no terminal. Understanding browser-based CV unlocks:

* Shareable demos : send a URL, anyone can run your CV app on their phone or laptop
* No-server deployment : TF.js (Unit 3) runs full neural networks in the browser tab
* Real-time dashboards : the JS frontend that displays your Python detector's output (Unit 6)
* Edge devices : browser CV runs on phones, tablets, Raspberry Pi browsers without Python

<table data-header-hidden><thead><tr><th width="271">Python + OpenCV (S3)</th><th>Browser JS + Canvas (S4)</th></tr></thead><tbody><tr><td>Needs Python installed + venv</td><td>Runs in any browser - zero install</td></tr><tr><td>cv2.VideoCapture(0)</td><td>navigator.mediaDevices.getUserMedia()</td></tr><tr><td>cv2.imshow() window</td><td>&#x3C;video> or &#x3C;canvas> element on a webpage</td></tr><tr><td>cv2.waitKey() loop</td><td>requestAnimationFrame() animation loop</td></tr><tr><td>frame[y, x] → BGR pixel</td><td>ctx.getImageData(x,y,1,1) → RGBA pixel</td></tr><tr><td>cv2.putText() annotation</td><td>ctx.fillText() / strokeText() annotation</td></tr><tr><td>Local only by default</td><td>Shareable via a URL - runs anywhere</td></tr></tbody></table>
