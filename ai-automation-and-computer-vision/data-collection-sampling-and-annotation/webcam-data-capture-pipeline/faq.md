# FAQ

### Common Errors & Fixes

<table><thead><tr><th width="233">Error</th><th>Cause</th><th>Fix</th></tr></thead><tbody><tr><td>No frames saved after 1 minute</td><td>CAPTURE_INTERVAL = 0 (manual only) and spacebar not pressed</td><td>Set CAPTURE_INTERVAL = 0.5 for auto-capture, or remind student to press spacebar</td></tr><tr><td>All frames rejected (reject rate 100%)</td><td>BLUR_THRESHOLD too high for webcam/lighting conditions</td><td>Lower threshold to 40.0 and test. Print blur scores to console first to calibrate.</td></tr><tr><td>Folder not found error on imwrite</td><td>setup_folders() not called before run_capture()</td><td>Ensure paths = setup_folders(...) is the first line of run_capture()</td></tr><tr><td>CSV file empty (header only)</td><td>Log not flushed before close or frames all rejected</td><td>Call log_f.flush() every 10 saves. Check reject folder for clues on why frames fail.</td></tr><tr><td>Duplicate frames filling the folder</td><td>MIN_DIFF too low (set to 0 or very small value)</td><td>Set MIN_DIFF = 500 as baseline. Increase if static scenes produce near-duplicates.</td></tr><tr><td>Preview window flickers or freezes</td><td>draw_overlay() modifying original frame (not copy)</td><td>Ensure preview = frame.copy() is called BEFORE draw_overlay(preview, stats)</td></tr></tbody></table>

<br>
