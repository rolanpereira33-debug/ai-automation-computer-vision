---
description: Not listed here? then let me know.
icon: wrench
---

# Troubleshooting

<table data-header-hidden><thead><tr><th width="195">Error</th><th width="280">Cause</th><th>Fix</th></tr></thead><tbody><tr><td>NotAllowedError: Permission denied</td><td>Camera permission blocked in browser</td><td>Click camera icon in address bar → Allow. Or check chrome://settings/content/camera</td></tr><tr><td>Canvas stays black after start</td><td>videoEl.play() not awaited  video hasn't started</td><td>Ensure await videoEl.play() before calling requestAnimationFrame</td></tr><tr><td>getImageData throws SecurityError</td><td>Page opened as file:// — CORS restriction</td><td>Use Live Server to serve on localhost. Never open HTML files directly for CV work.</td></tr><tr><td>FPS shows Infinity or NaN</td><td>delta is 0 on first frame (prevTime not set)</td><td>Initialise prevTime = performance.now() before the loop, not inside drawFrame</td></tr><tr><td>Canvas shows mirror image</td><td>Webcam is front-facing  browser doesn't flip by default</td><td>Add ctx.scale(-1,1) then ctx.translate(-canvas.width,0) before drawImage to flip horizontally</td></tr><tr><td>Pixel values are all 0</td><td>getImageData called before frame drawn to canvas</td><td>Ensure ctx.drawImage(videoEl,...) runs before getImageData in drawFrame</td></tr><tr><td>Video element visible on page</td><td>CSS display:none missing on &#x3C;video></td><td>Add video { display: none; } to the &#x3C;style> block</td></tr></tbody></table>
