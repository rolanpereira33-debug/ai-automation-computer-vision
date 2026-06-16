# Code Snippets

### capture.py

Build this step by step. Each section is introduced separately before the next is added. The final script is production-ready - this exact file is used at the Field Trip

#### Step 1 - Imports & configuration

```python
import cv2
import os
import csv
import time
import numpy as np
from datetime import datetime
 
# ── CONFIGURATION — edit these before each session ───────────────────
CLASS_NAME      = 'player'      # 'player' | 'ball' | 'goal' | 'background'
DATASET_ROOT    = 'cv_dataset'   # root folder for all captures
CAPTURE_INTERVAL= 0.5            # seconds between auto-captures (0 = manual only)
BLUR_THRESHOLD  = 80.0           # Laplacian variance — lower = stricter blur filter
MIN_DIFF        = 500            # min pixel diff to avoid duplicate frames
CAMERA_INDEX    = 0              # webcam index — try 1 or 2 if 0 fails
SAVE_QUALITY    = 92             # JPEG quality 0–100 (92 = high quality, small file)


```

#### Step 2 - Folder setup

```py
def setup_folders(root: str, class_name: str) -> dict:
    """Create dataset folder structure and return path dict."""
    paths = {
        'capture' : os.path.join(root, 'raw_capture', class_name),
        'rejected': os.path.join(root, 'rejected', class_name),
        'logs'    : os.path.join(root, 'logs'),
    }
    for p in paths.values():
        os.makedirs(p, exist_ok=True)
    return paths
```

#### Step 3 - Helper functions (blur, naming, diff)

```py
def is_blurry(frame, threshold=BLUR_THRESHOLD) -> bool:
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    return cv2.Laplacian(gray, cv2.CV_64F).var() < threshold
 
def blur_score(frame) -> float:
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    return round(cv2.Laplacian(gray, cv2.CV_64F).var(), 1)
 
def make_filename(class_name, idx) -> str:
    ts = datetime.now().strftime('%Y%m%d_%H%M%S')
    return f'{class_name}_{ts}_{str(idx).zfill(4)}.jpg'
 
def is_duplicate(frame, prev_frame, min_diff=MIN_DIFF) -> bool:
    if prev_frame is None: return False
    diff = np.sum(np.abs(frame.astype('int32') - prev_frame.astype('int32')))
    return diff < min_diff

```

#### Step 4 - Overlay drawing

```py
def draw_overlay(frame, stats: dict) -> None:
    """Draw live stats banner on the preview frame (does not affect saved images)."""
    h, w = frame.shape[:2]
    cv2.rectangle(frame, (0, 0), (w, 50), (20,20,60), -1)
 
    # Class name
    cv2.putText(frame, f'CLASS: {stats["class"]}',
        (10, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.55, (100,200,255), 1, cv2.LINE_AA)
 
    # Saved count
    cv2.putText(frame, f'Saved: {stats["saved"]} | Rejected: {stats["rejected"]}',
        (10, 38), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (166,227,161), 1, cv2.LINE_AA)
 
    # Blur score — colour-coded
    score  = stats['blur']
    colour = (0,255,0) if score > BLUR_THRESHOLD else (0,100,255)
    cv2.putText(frame, f'Blur: {score}',
        (w-140, 20), cv2.FONT_HERSHEY_SIMPLEX, 0.5, colour, 1, cv2.LINE_AA)
 
    # Instructions
    cv2.putText(frame, 'SPACE=capture  Q=quit',
        (w-200, h-10), cv2.FONT_HERSHEY_SIMPLEX, 0.42, (150,150,150), 1, cv2.LINE_AA)

```

#### Step 5 - CSV session log

```python
def init_log(log_dir: str, class_name: str) -> tuple:
    """Open a CSV log file and return (file_handle, csv_writer)."""
    ts       = datetime.now().strftime('%Y%m%d_%H%M%S')
    log_path = os.path.join(log_dir, f'session_{class_name}_{ts}.csv')
    f        = open(log_path, 'w', newline='')
    writer   = csv.writer(f)
    writer.writerow(['filename','class','timestamp','blur_score','status','width','height'])
    return f, writer
```

#### Step 6 - Main capture loop

```python
def run_capture():
    paths      = setup_folders(DATASET_ROOT, CLASS_NAME)
    log_f, log = init_log(paths['logs'], CLASS_NAME)
    cap        = cv2.VideoCapture(CAMERA_INDEX)
 
    if not cap.isOpened():
        print('ERROR: Cannot open webcam')
        return
 
    stats = {'class': CLASS_NAME, 'saved': 0, 'rejected': 0, 'blur': 0}
    prev_frame  = None
    last_save   = 0.0
    frame_idx   = 0
 
    print(f'Capturing class: {CLASS_NAME}. SPACE=manual save  Q=quit')
 
    while True:
        ret, frame = cap.read()
        if not ret: break
 
        h, w = frame.shape[:2]
        score  = blur_score(frame)
        stats['blur'] = score
 
        # ── Decide whether to auto-save ───────────────────────────────
        now         = time.time()
        time_ok     = CAPTURE_INTERVAL > 0 and now - last_save >= CAPTURE_INTERVAL
        quality_ok  = not is_blurry(frame) and not is_duplicate(frame, prev_frame) 
        res_ok      = w >= 640 and h >= 480
 
        key = cv2.waitKey(1) & 0xFF
        manual_save = key == ord(' ')  # spacebar
 
        if (time_ok or manual_save) and quality_ok and res_ok:
            fname    = make_filename(CLASS_NAME, frame_idx)
            fpath    = os.path.join(paths['capture'], fname)
            cv2.imwrite(fpath, frame, [cv2.IMWRITE_JPEG_QUALITY, SAVE_QUALITY])
            log.writerow([fname, CLASS_NAME, datetime.now().isoformat(), score, 'saved', w, h])
            stats['saved'] += 1
            prev_frame = frame.copy()
            last_save  = now
            frame_idx  += 1
 
        elif (time_ok or manual_save) and not quality_ok:
            # Save rejected frames for review
            rpath = os.path.join(paths['rejected'], make_filename('rej', frame_idx))
            cv2.imwrite(rpath, frame)
            log.writerow([os.path.basename(rpath), CLASS_NAME, datetime.now().isoformat(), score, 'rejected', w, h])
            stats['rejected'] += 1
            last_save = now
 
        preview = frame.copy()
        draw_overlay(preview, stats)
        cv2.imshow(f'Capture — {CLASS_NAME}', preview)
 
        if key == ord('q'): break
 
    # ── Cleanup ──────────────────────────────────────────────────────────
    cap.release()
    log_f.close()
    cv2.destroyAllWindows()
    print(f'Done. Saved: {stats["saved"]}  Rejected: {stats["rejected"]}  Accept rate: {stats["saved"] / max(1, stats["saved"] + stats["rejected"]) * 100:.0f}%')
 
if __name__ == '__main__':
    run_capture()
```
