# Webcam Data Capture Pipeline

### Learning Objectives

* Design a folder structure for a sports CV dataset
* Write a Python script that opens a webcam and captures frames at timed intervals
* Generate automatic, descriptive filenames using timestamps and class labels
* Implement a blur detection filter to reject low-quality frames before saving
* Add a manual capture trigger (spacebar) alongside automatic interval capture
* Display a live capture preview with frame count and FPS overlay
* Generate a capture session log as a CSV file for dataset tracking



### Quality Rules in Practice

Every quality issue identified in your analusis has a direct engineering solution in this session's capture script. You should see this as the practical implementation of what they audited.

| Class imbalance        | Separate folders per class — easy to count and balance                        | Per-class subdirectory creation + counter display |
| ---------------------- | ----------------------------------------------------------------------------- | ------------------------------------------------- |
| Motion blur            | Laplacian variance blur detection — reject frames below threshold             | cv2.Laplacian() blur filter before save           |
| Distribution bias      | Capture log records time, lighting, location — flag for diversity review      | CSV session log with metadata per frame           |
| Low resolution         | Check frame dimensions before saving — reject undersized frames               | Resolution guard: if w < 640 or h < 480: skip     |
| Null/duplicate frames  | Skip identical consecutive frames using pixel difference check                | Frame diff threshold: skip if diff < min\_diff    |
| Label noise prevention | Save frames with class subfolder and visible preview — annotator sees context | Class-labelled folder structure + preview window  |

<br>
