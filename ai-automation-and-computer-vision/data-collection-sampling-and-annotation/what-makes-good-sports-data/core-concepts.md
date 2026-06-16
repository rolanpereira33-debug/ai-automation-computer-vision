---
icon: qrcode
---

# Core Concepts

### The 7 Data Quality Issues

| Class Imbalance                                                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **What it is**:  One class has far more examples than others. In a football dataset: 500 images of players, 50 images of the ball, 10 images of the goal.        |
| **Impact on model:**  The model becomes expert at detecting players but nearly blind to the ball and goal. Accuracy looks high overall — but that is misleading. |
| **Sports example:**  A dataset collected during open play will have far more player detections than goal detections. Goals are rare events.                      |

| **What it is:**  Images are annotated incorrectly — wrong class assigned, bounding box too loose or too tight, or objects missed entirely.               |
| -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Impact on model:**  The model learns from wrong answers. A bounding box that cuts off half the player teaches the model that half-players are players. |
| **Sports example:**  Annotating quickly at a football match: labelling a linesman as a referee, or drawing a box around two overlapping players as one.  |

| **What it is:** Data comes from a narrow range of conditions. All images are from one stadium, one time of day, one weather condition, or one camera angle. |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Impact on model:** The model only works in the conditions it was trained on. Deploy it at a different ground or at night and accuracy drops sharply.      |
| **Sports example:**  Training only on daytime Maldives beach football model fails at an indoor futsal court under fluorescent lighting.                     |

| **What it is:** Fast-moving objects the ball, sprinting players produce blurred frames that are partially or fully unrecognisable.                   |
| ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Impact on model:**  Blurred ball frames with confident labels teach the model to detect blur as a ball. At inference time it may miss sharp balls. |
| **Sports example:**  A ball kicked at speed appears as a smear across 3–4 pixels. If labelled, the label is geometrically meaningless.               |

| What it is:  Objects overlap — one player obscures another, the ball is behind a leg, a referee blocks the goal.                                        |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Impact on model:  The model may learn partial views as complete objects, or fail entirely when occlusion is present at inference.                       |
| Sports example:  A corner kick: 5 players tightly packed. Each is partially visible. Labelling each individual requires careful partial-box annotation. |

| What it is:  Images cover a very narrow brightness range — all shot in good afternoon light, none at dusk, floodlit, or in overcast conditions.                |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Impact on model:  The model fails when real-world lighting differs from training conditions. Colours shift dramatically under different light sources.         |
| Sports example:  A detector trained on bright afternoon pitch footage fails at a night match under yellow stadium floodlights — the green pitch reads as gold. |

| What it is:  Images are too small, compressed, or blurry to contain useful visual information. Common with consumer webcams at distance.          |
| ------------------------------------------------------------------------------------------------------------------------------------------------- |
| Impact on model:  The model learns from noise rather than features. Fine-detail classes (ball, player number, referee whistle) cannot be learned. |
| Sports example:  Webcam footage of a full pitch: the ball may be just 4×4 pixels — indistinguishable from dirt on the lens.                       |

### Quality vs Quantity  - The Central Trade-off

We often believe more data always means a better model. This is a persistent misconception worth addressing directly.

| 2,000 blurry, mislabelled frames                               | 200 sharp, correctly annotated frames                           |
| -------------------------------------------------------------- | --------------------------------------------------------------- |
| Trained on 1 stadium, 1 lighting condition                     | Trained across 5 stadiums, day + night + overcast               |
| 500 players, 10 balls — imbalanced                             | 100 of each class balanced                                      |
| Labels drawn loosely around groups                             | Labels tight and consistent around individuals                  |
| Model reaches 85% accuracy on training set  fails in the field | Model reaches 72% accuracy but works reliably at the field trip |

### Reading Roboflow's Dataset Health Check

Roboflow automatically analyses every uploaded dataset and flags health issues. You need to be able to read and interpret this dashboard before the field trip

<table data-header-hidden><thead><tr><th width="197">Roboflow indicator</th><th>What it flags</th><th>Action to take</th></tr></thead><tbody><tr><td>Class balance chart</td><td>Bar chart shows instances per class. Look for bars that are 10x taller than others.</td><td>Collect more examples of underrepresented classes or remove excess from dominant class.</td></tr><tr><td>Null examples</td><td>Images with no annotations the model sees these as 'background only'.</td><td>Delete or re-annotate. Too many null examples teaches the model to ignore real objects.</td></tr><tr><td>Annotation heatmap</td><td>Where on the image do labels cluster? If all balls are centre-frame, model won't detect edge balls.</td><td>Collect images with objects in varied positions: edges, corners, partial frames.</td></tr><tr><td>Duplicate images</td><td>Same image uploaded twice  inflates dataset size without adding information.</td><td>Enable Roboflow's deduplication  it removes pixel-identical and near-duplicate frames.</td></tr><tr><td>Image size distribution</td><td>Images at very different resolutions in the same dataset cause inconsistent training.</td><td>Resize all images to a consistent resolution (640×640 for YOLOv8) before training.</td></tr><tr><td>Split preview</td><td>Check train/val/test split is roughly 70/20/10 and that each split has all classes represented.</td><td>If one class is absent from val, model cannot be evaluated on it. Reshuffle the split.</td></tr></tbody></table>

\
<br>
