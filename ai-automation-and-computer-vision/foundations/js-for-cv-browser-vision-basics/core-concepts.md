---
icon: qrcode
---

# Core Concepts

### navigator.mediaDevices.getUserMedia()

**getUserMedia()** is the browser's **permission-gated API** for accessing cameras and microphones. It returns a Promise that resolves to a MediaStream ( a live video stream from the webcam). The browser always asks the user for permission first.

| (Imagine this model being imposed on students talking during class. Being a teacher I would prefer hitting <mark style="color:$danger;">**deny**</mark>) |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| • The browser asks once per origin (domain). After allowing, it remembers.                                                                               |
| • File:// pages (opening an HTML file directly) may be blocked - always use Live Server.                                                                 |
| • HTTPS is required in production. Live Server uses localhost which is always allowed.                                                                   |
| • If a student sees 'Permission denied': click the camera icon in the address bar → Allow.                                                               |

```js
// Constraints object — tells the browser what we want
const constraints = {
  video: {
    width:  { ideal: 1280 },
    height: { ideal: 720 },
    facingMode: 'user'  // 'user' = front cam, 'environment' = rear
  },
  audio: false
};
 
// async function — getUserMedia returns a Promise
async function startWebcam() {
  try {
    const stream = await navigator.mediaDevices.getUserMedia(constraints);
    videoEl.srcObject = stream;       // attach stream to <video>
    await videoEl.play();            // start playing
    requestAnimationFrame(drawFrame);  // start render loop
  } catch (err) {
    console.error('Webcam error: ', err);
    document.getElementById('status').textContent = 'ERROR: ' + err.message;
  }
}

```

***

### HTML5 Canvas - Drawing Frames

The Canvas API gives us a drawing surface in the browser. Unlike a \<video> element, canvas lets us read, modify, and annotate pixels before displaying them exactly like a frame in OpenCV.

```js
// Get references to DOM elements
const videoEl = document.getElementById('webcam');
const canvas  = document.getElementById('canvas');
const ctx     = canvas.getContext('2d');  // 2D rendering context
 
function drawFrame() {
  // Match canvas size to video stream
  canvas.width  = videoEl.videoWidth;
  canvas.height = videoEl.videoHeight;
 
  // Draw the current video frame onto the canvas
  ctx.drawImage(videoEl, 0, 0, canvas.width, canvas.height);
 
  // === YOUR ANNOTATIONS GO HERE ===
 
  // Loop — request the next frame
  requestAnimationFrame(drawFrame);
}

```

***

### Reading Pixel Values with ctx.getImageData()

In OpenCV we used frame\[y, x] to get a BGR pixel. In Canvas we use **ctx.getImageData(x, y, width, height)** which returns an ImageData object containing a flat Uint8ClampedArray of RGBA values.

| Canvas  →  RGBA  (Red, Green, Blue, Alpha)  ⇒ 4 values per pixel, Alpha = opacity 0–255                      |
| ------------------------------------------------------------------------------------------------------------ |
| OpenCV  →  BGR   (Blue, Green, Red)             ⇒ 3 values per pixel, no alpha channel                       |
| Key difference: Canvas uses RGBA in Red-first order. OpenCV uses BGR in Blue-first order.                    |
| To compare: Canvas R=pixel\[0], G=pixel\[1], B=pixel\[2]   vs   OpenCV B=pixel\[0], G=pixel\[1], R=pixel\[2] |

```js
canvas.addEventListener('click', (e) => {
 
  // Get click position relative to canvas
  const rect = canvas.getBoundingClientRect();
  const x    = Math.round(e.clientX - rect.left);
  const y    = Math.round(e.clientY - rect.top);
 
  // Extract one pixel (1x1 region) at (x, y)
  const px   = ctx.getImageData(x, y, 1, 1).data;
  // px is a Uint8ClampedArray: [R, G, B, A]
 
  const r = px[0];  const g = px[1];  const b = px[2];
 
  // Display pixel info on the page
  document.getElementById('pixel-info').textContent 
    = `Pixel (${x}, ${y})  →  R:${r}  G:${g}  B:${b}`;
 
  // Draw a crosshair at the click point
  ctx.strokeStyle = '#00FF00';
  ctx.lineWidth = 2;
  ctx.beginPath(); ctx.moveTo(x-10, y); ctx.lineTo(x+10, y); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(x, y-10); ctx.lineTo(x, y+10); ctx.stroke();
});



```

***

### Text & Shape Overlays - ctx.fillText() and ctx.fillRect()

Canvas has a rich drawing API. Every annotation we draw in this course FPS counters, bounding boxes, zone outlines, labels uses these same primitives.

```js
// ── Text overlay ─────────────────────────────────────
ctx.font = 'bold 20px Arial';
ctx.fillStyle = '#00FF00';   // green
ctx.fillText('FPS: 30', 10, 30);
 
// Add a dark stroke behind text for readability
ctx.strokeStyle = '#000000';
ctx.lineWidth = 3;
ctx.strokeText('FPS: 30', 10, 30); // draw stroke FIRST, fill on top
ctx.fillText('FPS: 30', 10, 30);
 
// ── Filled rectangle (dark banner) ───────────────────
ctx.fillStyle = 'rgba(20,20,60,0.75)';  // semi-transparent navy
ctx.fillRect(0, 0, canvas.width, 44);
 
// ── Stroked rectangle (bounding box) ────────────────
ctx.strokeStyle = '#FF3B3B';
ctx.lineWidth = 2;
ctx.strokeRect(x, y, width, height);  // draws a box — used for detections later
```

### requestAnimationFrame() - The Right Way to Loop

Do not use setInterval() for video loops. requestAnimationFrame() (rAF) synchronises with the browser's refresh cycle (\~60fps), pauses when the tab is hidden (saving CPU), and produces smoother results. It is the direct JS equivalent of OpenCV's while True: loop.

| Runs even when tab is hidden — wastes CPU  | Automatically pauses when tab is backgrounded |
| ------------------------------------------ | --------------------------------------------- |
| Fixed interval — can't adapt to frame rate | Syncs to display refresh rate (\~60fps)       |
| Can pile up calls if code is slow          | Waits for previous frame to finish            |
| setInterval(drawFrame, 33)                 | requestAnimationFrame(drawFrame)              |
