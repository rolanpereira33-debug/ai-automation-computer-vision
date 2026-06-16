---
icon: qrcode
---

# Core Concepts

### Dataset Folder Structure

A well-organised folder structure makes annotation, splitting, and training far easier. For sports CV, organise by class from the start; this maps directly to Roboflow's upload structure.

\
&#x20; PY    Recommended folder layout for a sports dataset

| ├── raw\_capture/          # everything captured by the script  |
| --------------------------------------------------------------- |
| │   ├── player/           # frames where a player is visible    |
| │   ├── ball/             # frames featuring the ball clearly   |
| │   ├── goal/             # frames showing the goal             |
| │   └── background/       # pitch/crowd with no target objects  |
| ├── annotated/            # Roboflow-annotated export goes here |
| ├── logs/                 # CSV capture logs                    |
| └── rejected/             # blurry/duplicate frames for review  |

| When you upload to Roboflow, you assign class labels during annotation ... not at capture. But sorting into class folders at capture time means you always know what is in each folder,   making it easy to count, balance, and add to any specific class during field trips. It also means the annotator has context: opening the 'ball' folder, they expect to label balls. |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### Blur Detection with Laplacian Variance

The sharpness of an image can be measured using the variance of the Laplacian — a second-order derivative that responds strongly to edges. A sharp image has many strong edges; a blurry image has weak, spread-out edges. Low variance = blurry = reject

```python
def is_blurry(frame, threshold=80.0) -> bool:
    """
    Returns True if the frame is too blurry to use.
    threshold: lower = stricter. 80 works well for webcam footage.
    Typical values: sharp indoor = 200+, outdoor motion = 60-120, blurry = <40
    """
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    variance = cv2.Laplacian(gray, cv2.CV_64F).var()
    return variance < threshold
 
def blur_score(frame) -> float:
    """Return the raw sharpness score for display."""
    gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
    return round(cv2.Laplacian(gray, cv2.CV_64F).var(), 1)

```

| Static scene, good light     | 150–400 | Accept - excellent for annotation         |
| ---------------------------- | ------- | ----------------------------------------- |
| Slow-moving player           | 80–150  | Accept - usable quality                   |
| Fast sprint / ball in flight | 30–80   | Borderline - adjust threshold per session |
| Camera shake / very fast     | < 30    | Reject - no useful information            |

### Automatic File Naming Convention

Good filenames contain enough information to understand a frame without opening it. Use timestamps for uniqueness and class labels for organisation.

```py
from datetime import datetime
 
def make_filename(class_name: str, frame_idx: int) -> str:
    """
    Generates: player_20250610_143022_0042.jpg
    Format: {class}_{date}_{time}_{index:04d}.jpg
    """
    ts  = datetime.now().strftime('%Y%m%d_%H%M%S')
    idx = str(frame_idx).zfill(4)  # zero-pad to 4 digits
    return f'{class_name}_{ts}_{idx}.jpg'
 
# Examples:
# player_20250610_143022_0042.jpg
# ball_20250610_144115_0007.jpg
# goal_20250610_145301_0001.jpg



```
