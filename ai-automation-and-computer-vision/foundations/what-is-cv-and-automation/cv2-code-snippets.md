---
icon: face-glasses
---

# cv2  Code Snippets



<pre class="language-py"><code class="lang-py">
import cv2    
<strong># a library that gives Python the ability to read cameras and process images
</strong>import numpy as np
<strong># NumPy handles the maths. Images are just big grids of numbers 
</strong>
cap = cv2.VideoCapture(0)
<strong># This opens the webcam
</strong>
ret, frame = cap.read()
<strong># This grabs one frame. ret tells us if it worked; frame is the actual image data
</strong>
print(frame.shape)
<strong># This prints the shape of the image : height, width, and colour channels. A typical webcam gives you (480, 640, 3): 480 rows, 640 columns, 3 colours (blue, green, red).
</strong>
print(frame[240, 320])
<strong># This prints the single pixel at the centre of the frame just three numbers: Blue, Green, Red values from 0 to 255. That's all an image is.
</strong>
cap.release()
<strong># Always release the camera when you are done, or it stays locked
</strong></code></pre>
