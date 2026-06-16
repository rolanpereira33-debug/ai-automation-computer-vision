---
icon: code
---

# Code Snippets

webcam\_lab.py

```python
import cv2
import numpy as np
import time
 
print(f'OpenCV version: {cv2.__version__}')
print(f'NumPy  version: {np.__version__}')
# Expected output:
# OpenCV version: 4.x.x
# NumPy  version: 1.x.x or 2.x.x
```

{% hint style="info" %}
**Run this first**&#x20;

If cv2 imports without error and prints a version number, your install is working. If you see ModuleNotFoundError: check your venv is activated (look for '(venv)' in the prompt). If the version prints but is below 4.5.0 - run: **pip install --upgrade opencv-python**
{% endhint %}

### Core webcam loop

```python
cap = cv2.VideoCapture(0)
 
if not cap.isOpened():
    print('ERROR: Cannot open webcam. Check camera index.')
    exit()
 
print('Webcam opened. Press Q to quit.')
 
while True:
    ret, frame = cap.read()
    if not ret:
        print('ERROR: Frame not received.')
        break
 
    cv2.imshow('S3 — Webcam Feed', frame)
 
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break
 
cap.release()
cv2.destroyAllWindows()

```

#### FPS counter & resolution overlay

Replace the imshow() line (and add before it) with the following. This goes inside the while loop, after frame = cap.read().

```python
    # ── FPS calculation ──────────────────────────────
    if 'prev_time' not in dir():
        prev_time = time.time()
 
    curr_time = time.time()
    fps = 1.0 / (curr_time - prev_time + 1e-9)
    prev_time = curr_time
 
    h, w = frame.shape[:2]
 
    # ── Draw dark banner at top ───────────────────────
    cv2.rectangle(frame,
        (0, 0), (w, 40),
        (20, 20, 60), -1)  # filled rectangle — NAVY colour
 
    # ── FPS text ─────────────────────────────────────
    cv2.putText(frame,
        f'FPS: {fps:.1f}',
        (10, 28),
        cv2.FONT_HERSHEY_SIMPLEX, 0.7,
        (0, 255, 0), 2, cv2.LINE_AA)
 
    # ── Resolution text ──────────────────────────────
    cv2.putText(frame,
        f'{w}x{h}',
        (w - 110, 28),
        cv2.FONT_HERSHEY_SIMPLEX, 0.7,
        (200, 200, 200), 1, cv2.LINE_AA)
 
    cv2.imshow('S3 — Webcam Feed', frame)
```

### Extension: grayscale toggle on key press 'G'

```python
    # ── Add BEFORE imshow(), inside the while loop ──
    key = cv2.waitKey(1) & 0xFF
 
    if key == ord('g'):
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        frame = cv2.cvtColor(gray, cv2.COLOR_GRAY2BGR)
        cv2.putText(frame, 'GREYSCALE', (w//2 - 70, h//2),
            cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 255), 2, cv2.LINE_AA)
 
    if key == ord('q'):
        break

```

### Extension: live HSV pitch mask (from Session 2)

```python
    # ── Press 'h' to toggle HSV green mask ──────────
    if key == ord('h'):
        hsv         = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
        lower_green = np.array([35, 50, 50])
        upper_green = np.array([85, 255, 255])
        mask        = cv2.inRange(hsv, lower_green, upper_green)
        frame       = cv2.bitwise_and(frame, frame, mask=mask)
        cv2.putText(frame, 'HSV MASK — GREEN', (10, h-15),
            cv2.FONT_HERSHEY_SIMPLEX, 0.7, (0, 255, 0), 2, cv2.LINE_AA)
```
