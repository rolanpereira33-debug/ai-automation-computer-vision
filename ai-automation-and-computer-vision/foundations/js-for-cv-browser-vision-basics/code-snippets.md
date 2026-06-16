---
icon: code
---

# Code Snippets

One single HTML file  no external dependencies, no npm, no server required (use Live Server to serve it locally). Because i know you know that we dont want to wait for the npm installation again.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>CV Browser — S4</title>
  <style>
    body { margin: 0; background: #0f0f1a; color: #cdd6f4; font-family: Arial, sans-serif; }
    h1   { color: #89b4fa; text-align: center; padding: 12px 0 4px; }
    .container { display: flex; flex-direction: column; align-items: center; gap: 10px; }
    canvas  { border: 2px solid #1a1a5e; border-radius: 6px; }
    video   { display: none; }  /* hidden — used as source only */
    #status, #pixel-info { font-size: 14px; color: #a6e3a1; min-height: 22px; }
    button  { padding: 8px 20px; background: #1a1a5e; color: #fff; border: none; border-radius: 4px; cursor: pointer; }
    button:hover { background: #2d2d7a; }
  </style>
</head>
 
<body>
  <h1>S4 — Browser CV: Live Webcam Feed</h1>
  <div class="container">
    <!-- Hidden video element — receives the webcam stream -->
    <video id="webcam" autoplay playsinline></video>
    <!-- Canvas — this is what the user sees -->
    <canvas id="canvas"></canvas>
    <p id="status">Waiting for webcam…</p>
    <p id="pixel-info">Click on canvas to sample a pixel</p>
    <button onclick="startWebcam()">▶ Start Webcam</button>
    <button onclick="stopWebcam()">■ Stop</button>
  </div>
 
<script>
// ── Element references ───────────────────────────────
const videoEl    = document.getElementById('webcam');
const canvas     = document.getElementById('canvas');
const ctx        = canvas.getContext('2d');
const statusEl   = document.getElementById('status');
const pixelInfoEl= document.getElementById('pixel-info');
 
// ── State ───────────────────────────────────────────
let stream     = null;
let rafId      = null;  // requestAnimationFrame ID — used to cancel the loop
let prevTime   = performance.now();
let frameCount = 0;
 
// ── Start webcam ────────────────────────────────────
async function startWebcam() {
  try {
    stream = await navigator.mediaDevices.getUserMedia({ video: { width:1280, height:720}, audio:false });
    videoEl.srcObject = stream;
    await videoEl.play();
    statusEl.textContent = '✅ Webcam active — click canvas to sample pixels';
    rafId = requestAnimationFrame(drawFrame);
  } catch (err) {
    statusEl.textContent = '❌ Error: ' + err.message;
    console.error(err);
  }
}
 
// ── Stop webcam ─────────────────────────────────────
function stopWebcam() {
  if (rafId) cancelAnimationFrame(rafId);
  if (stream) stream.getTracks().forEach(t => t.stop());
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  statusEl.textContent = 'Webcam stopped.';
}
 
// ── Main render loop ────────────────────────────────
function drawFrame(timestamp) {
 
  // FPS calculation
  const delta = timestamp - prevTime;
  const fps   = (1000 / delta).toFixed(1);
  prevTime = timestamp;
  frameCount++;
 
  // Draw the current video frame onto the canvas
  canvas.width  = videoEl.videoWidth || 1280;
  canvas.height = videoEl.videoHeight || 720;
  ctx.drawImage(videoEl, 0, 0, canvas.width, canvas.height);
 
  // ── Dark banner overlay ────────────────────────────
  ctx.fillStyle = 'rgba(20,20,60,0.72)';
  ctx.fillRect(0, 0, canvas.width, 44);
 
  // ── FPS counter ─────────────────────────────────────
  ctx.font = 'bold 18px Arial';
  ctx.fillStyle = '#A6E3A1';  // mint green
  ctx.fillText(`FPS: ${fps}`, 12, 28);
 
  // ── Resolution ───────────────────────────────────────
  ctx.fillStyle = '#CDD6F4';
  ctx.fillText(`${canvas.width} × ${canvas.height}`, canvas.width - 130, 28);
 
  // ── Frame counter ───────────────────────────────────
  ctx.fillStyle = '#6C7086';
  ctx.font = '13px Arial';
  ctx.fillText(`Frame #${frameCount}`, 12, canvas.height - 10);
 
  // Loop — schedule the next frame
  rafId = requestAnimationFrame(drawFrame);
}
 
// ── Pixel sampler — click event ─────────────────────
canvas.addEventListener('click', (e) => {
  const rect = canvas.getBoundingClientRect();
  const scaleX= canvas.width  / rect.width;
  const scaleY= canvas.height / rect.height;
  const x = Math.round((e.clientX - rect.left) * scaleX);
  const y = Math.round((e.clientY - rect.top) * scaleY);
  const px = ctx.getImageData(x, y, 1, 1).data;
  const hex = '#' + [px[0],px[1],px[2]].map(v=>v.toString(16).padStart(2,'0')).join('');
  pixelInfoEl.textContent =
    `📍 (${x}, ${y})  R:${px[0]}  G:${px[1]}  B:${px[2]}  A:${px[3]}  hex: ${hex}`;
  // Draw crosshair
  ctx.strokeStyle = '#A6E3A1'; ctx.lineWidth = 1.5;
  ctx.beginPath(); ctx.moveTo(x-12,y); ctx.lineTo(x+12,y); ctx.stroke();
  ctx.beginPath(); ctx.moveTo(x,y-12); ctx.lineTo(x,y+12); ctx.stroke();
});
</script>
</body>
</html>




```

***

### Extension Tasks

Each extension is self-contained and can be added to cv\_browser.html.

#### **Extension A**: Snapshot button  save a frame as PNG

Add a 'Take Snapshot' button. When clicked, export the current canvas frame as a PNG download.

```javascript
function takeSnapshot() {
  const link = document.createElement('a');
  link.download = `snapshot_${Date.now()}.png`;
  link.href     = canvas.toDataURL('image/png');
  link.click();
}
```

***

#### Extension B: Grayscale toggle pixel manipulation

Add a checkbox that converts each frame to grayscale by iterating over all pixels in the ImageData array.

```js
function applyGrayscale(ctx, w, h) {
  const imageData = ctx.getImageData(0, 0, w, h);
  const data = imageData.data;
  for (let i = 0; i < data.length; i += 4) {
    const avg = (data[i]*0.299 + data[i+1]*0.587 + data[i+2]*0.114);
    data[i] = data[i+1] = data[i+2] = avg;
  }
  ctx.putImageData(imageData, 0, 0);
}
```

#### Extension C: Green pitch detector - live colour filter

Loop over every pixel and zero-out any pixel whose green channel is NOT dominant - isolating the pitch. Note: this is CPU-intensive; runs at \~10fps on full resolution.

```javascript
function isolateGreen(ctx, w, h) {
  const d = ctx.getImageData(0,0,w,h).data;
  for (let i=0; i<d.length; i+=4) {
    const g=d[i+1], r=d[i], b=d[i+2];
    if (!(g > r+20 && g > b+20 && g > 80))   // not green enough
      d[i]=d[i+1]=d[i+2]=0;             // black out the pixel
  }
  ctx.putImageData(new ImageData(d,w,h), 0,0);
}


```
