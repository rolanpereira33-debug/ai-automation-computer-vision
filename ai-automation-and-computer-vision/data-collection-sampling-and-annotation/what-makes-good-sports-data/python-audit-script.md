---
icon: code
---

# Python Audit Script

After exploring Roboflow visually, whoever finishes early run this Python script on a downloaded YOLO-format dataset. It produces a class distribution bar chart and flags potential issues automatically.

Ask rolan37 for the data. There should be some football players, refrees, balls and goal. Only then you will be able to conduct the audit on your dataset.

```py
//dataset_audit.py

import os, glob
import numpy as np
import matplotlib.pyplot as plt
from collections import Counter
 
# ── CONFIG ───────────────────────────────────────────────────────────
LABELS_DIR  = 'dataset/train/labels'  # path to YOLO .txt label files
CLASS_NAMES = ['player', 'ball', 'goal', 'referee']
 
# ── PARSE LABELS ─────────────────────────────────────────────────────
label_files   = glob.glob(os.path.join(LABELS_DIR, '*.txt'))
all_classes   = []
empty_files   = []   # null examples
total_objects = 0
 
for f in label_files:
    with open(f) as fh:
        lines = fh.readlines()
        if not lines:
            empty_files.append(f)
        for line in lines:
            cls = int(line.split()[0])
            all_classes.append(cls)
            total_objects += 1
 
# ── CLASS DISTRIBUTION ──────────────────────────────────────────────
counts = Counter(all_classes)
print(f'\n📊 DATASET AUDIT REPORT')
print(f'Total images : {len(label_files)}')
print(f'Total objects: {total_objects}')
print(f'Null examples: {len(empty_files)} ({len(empty_files)/len(label_files)*100:.1f}%)')
print('\nClass counts:')
for idx, name in enumerate(CLASS_NAMES):
    n = counts.get(idx, 0)
    bar = '█' * (n // 10)
    print(f'  {name:<12} {n:>4}  {bar}')
 
# ── IMBALANCE CHECK ─────────────────────────────────────────────────
if counts:
    max_c = max(counts.values())
    min_c = min(counts.values())
    ratio = max_c / (min_c + 1e-9)
    if ratio > 5:
        print(f'\n⚠️  CLASS IMBALANCE: max/min ratio = {ratio:.1f}x — investigate')
    if len(empty_files) / len(label_files) > 0.1:
        print(f'\n⚠️  NULL EXAMPLES > 10% — review or remove')
 
# ── BAR CHART ────────────────────────────────────────────────────────
names  = [CLASS_NAMES[i] for i in sorted(counts)]
values = [counts[i] for i in sorted(counts)]
colors = ['#89B4FA', '#F38BA8', '#A6E3A1', '#FAB387'][:len(names)]
 
plt.figure(figsize=(9, 5))
plt.bar(names, values, color=colors, edgecolor='#1E1E2E')
plt.title('Class Distribution — Sports CV Dataset', fontsize=14)
plt.xlabel('Class')
plt.ylabel('Instance count')
plt.axhline(y=max_c/5, color='red', linestyle='--', label='Minimum recommended (max/5)')
plt.legend()
plt.tight_layout()
plt.savefig('class_distribution.png', dpi=150)
plt.show()
print('\n✅ Chart saved as class_distribution.png')

```

| **DATASET AUDIT REPORT**                                |
| ------------------------------------------------------- |
| Total images : 312                                      |
| Total objects: 487                                      |
| Null examples: 14 (4.5%)                                |
|                                                         |
| Class counts:                                           |
|   player        312  ████████████████████████████████   |
|   ball           48  ████                               |
|   goal           87  ████████                           |
|   referee        40  ████                               |
|                                                         |
| ⚠️  CLASS IMBALANCE: max/min ratio = 7.8x - investigate |

