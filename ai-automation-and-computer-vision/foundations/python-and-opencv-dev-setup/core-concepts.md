---
icon: qrcode
---

# Core Concepts

### Virtual Environments - Why They Matter

A virtual environment is an isolated Python installation for one project. Without it, all your projects share the same packages which causes version conflicts that are very hard to debug. In this course, every project runs in its own environment.

{% hint style="info" icon="plane-departure" %}
<mark style="color:$danger;">**The Rule for this course**</mark>&#x20;

One project = one virtual environment. Always.&#x20;

Never install packages into the global Python installation.&#x20;

If you see '(venv)' at the start of your terminal prompt, your environment is active.
{% endhint %}

### Virtual Environment Cheat Sheet

<table><thead><tr><th valign="top">Windows</th><th valign="top">macOS</th><th valign="top">Linux</th></tr></thead><tbody><tr><td valign="top"><p># Create</p><p>python -m venv venv</p><p> </p><p># Activate</p><p>.\venv\Scripts\activate</p><p> </p><p># Deactivate</p><p>deactivate</p></td><td valign="top"><p># Create</p><p>python3 -m venv venv</p><p> </p><p># Activate</p><p>source venv/bin/activate</p><p> </p><p># Deactivate</p><p>deactivate</p></td><td valign="top"><p># Create</p><p>python3 -m venv venv</p><p> </p><p># Activate</p><p>source venv/bin/activate</p><p> </p><p># Deactivate</p><p>deactivate</p></td></tr></tbody></table>

***

### pip & Package Management

pip is Python's package installer. It downloads libraries from PyPI (the Python Package Index). Always install inside an active virtual environment if (venv) is not in your prompt, do not run pip.

```vb
# Verify pip is working inside the venv
pip --version
 
# Install OpenCV (includes both cv2 and contrib modules)
pip install opencv-python
 
# Install additional packages for this unit
pip install numpy matplotlib
 
# Save your environment to a requirements file
pip freeze > requirements.txt
 
# Recreate the environment on another machine
pip install -r requirements.txt

```

### VS Code - Recommended Setup (Pycharm is HEAVY)

VS Code is lightweight, free, and has excellent Python support. The key step after installing VS Code is pointing it to the correct Python interpreter (the one inside the venv, not the system Python).

&#x20;

<table><thead><tr><th width="266.800048828125" valign="top">Extension / Setting</th><th valign="top">Why it matters for this course</th></tr></thead><tbody><tr><td valign="top">Python (Microsoft)</td><td valign="top">Core extension — syntax highlighting, IntelliSense, linting, debugger. Install first.</td></tr><tr><td valign="top">Pylance</td><td valign="top">Type checking and autocomplete. Catches errors before you run the code.</td></tr><tr><td valign="top">Jupyter</td><td valign="top">Run .ipynb notebooks from inside VS Code — needed for S2 and data analysis sessions.</td></tr><tr><td valign="top">Select interpreter (Ctrl+Shift+P)</td><td valign="top">Always choose './venv/Scripts/python' (Win) or './venv/bin/python' (Mac/Linux) so VS Code uses your venv.</td></tr><tr><td valign="top">integrated terminal</td><td valign="top">Run python scripts.py and pip commands without leaving the editor.</td></tr><tr><td valign="top">Error Lens (optional)</td><td valign="top">Displays error messages inline next to the code — very useful for beginners.</td></tr></tbody></table>

OpenCV Webcam Essentials - The Core Loop

Every real-time CV application in this program is built on the same four-line loop. Understanding each line is non-negotiable before moving on.

```python
import cv2
 
cap = cv2.VideoCapture(0)
  # 0 = first webcam. Use 1 or 2 if you have multiple cameras.
 
while True:
    ret, frame = cap.read()
    # ret  = True if frame was read successfully
    # frame = the image as a NumPy array (H, W, 3) in BGR
 
    if not ret:
        break
 
    cv2.imshow('Webcam feed', frame)
 
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break  # press Q to quit
 
cap.release()
cv2.destroyAllWindows()
```

<table data-header-hidden><thead><tr><th width="219.59991455078125" valign="top">Code</th><th valign="top">What it does &#x26; why</th></tr></thead><tbody><tr><td valign="top">cv2.VideoCapture(0)</td><td valign="top">Opens the webcam at index 0. Returns a VideoCapture object that represents the camera stream. Use cap.isOpened() to check it worked.</td></tr><tr><td valign="top">cap.read()</td><td valign="top">Grabs the next frame. Returns (ret, frame). If the camera disconnects, ret becomes False always check it before using frame.</td></tr><tr><td valign="top">cv2.imshow('title', frame)</td><td valign="top">Opens a display window and shows the frame. The first argument is the window title use a descriptive name when you have multiple windows.</td></tr><tr><td valign="top">cv2.waitKey(1)</td><td valign="top">Waits 1ms for a key press. This is what drives the loop without it the window freezes. The &#x26; 0xFF is a bitwise mask for cross-platform key code compatibility.</td></tr><tr><td valign="top">cap.release()</td><td valign="top">Returns the webcam to the OS. Without this, the camera stays locked other apps cannot use it until you restart Python.</td></tr><tr><td valign="top">cv2.destroyAllWindows()</td><td valign="top">Closes every imshow() window. Always call this after release() or windows may hang.</td></tr></tbody></table>

### Overlaying Text - cv2.putText()

cv2.putText() draws text directly onto a frame before displaying it. This is how every annotation, label, FPS counter, and alert message is rendered in this course.

```python
cv2.putText(
    img          = frame,
    text         = 'Hello, CV!',
    org           = (30, 50),        # (x, y) in pixels from top-left
    fontFace      = cv2.FONT_HERSHEY_SIMPLEX,
    fontScale     = 1.2,               # size multiplier
    color         = (0, 255, 0),       # BGR — (0,255,0) = green
    thickness     = 2,               # stroke width in pixels
    lineType      = cv2.LINE_AA              # anti-aliased — always use this
)

```

<table><thead><tr><th valign="top">BGR colour quick reference</th></tr></thead><tbody><tr><td valign="top">Pure green →  (0, 255, 0)               Pure red    →  (0, 0, 255)                 Pure blue   →  (255, 0, 0)</td></tr><tr><td valign="top">White         →  (255, 255, 255)        Black         →  (0, 0, 0)                     Yellow        →  (0, 255, 255)</td></tr><tr><td valign="top"><strong>Remember: OpenCV is BGR not RGB — Red and Blue are swapped vs what you expect.</strong></td></tr></tbody></table>
