---
icon: alien-8bit
---

# Pixel Color Code Snippets

Each cell is a step. You run each cell in order, observe the output, then answer the question below each cell.

### Setup & load image

```py
import cv2
import numpy as np
import matplotlib.pyplot as plt

# Load the image (OpenCV reads as BGR)
img_bgr = cv2.imread('football_pitch.jpg')

# Convert to RGB for correct Matplotlib display
img_rgb = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2RGB)

print(f'Shape: {img_bgr.shape}')
print(f'Dtype: {img_bgr.dtype}')
print(f'Centre pixel BGR: {img_bgr[img_bgr.shape[0]//2, img_bgr.shape[1]//2]}')

plt.imshow(img_rgb)
plt.title('Original image')
plt.axis('off')
plt.show()

```

***

**Q: What is the shape of the image? What do the three numbers mean?**

### Split into RGB channels

```python
# Split BGR channels and display each
b_ch, g_ch, r_ch = cv2.split(img_bgr)

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
for ax, ch, title in zip(axes, [b_ch, g_ch, r_ch], ['Blue','Green','Red']):
    ax.imshow(ch, cmap='gray')
    ax.set_title(title)
    ax.axis('off')
plt.show()
```

***

**Q:Which channel looks brightest over the grass area — Blue, Green, or Red? Why does that make sense?**

### Convert to grayscale and HSV

<pre class="language-python"><code class="lang-python"># Grayscale
gray = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2GRAY)

# HSV
hsv = cv2.cvtColor(img_bgr, cv2.COLOR_BGR2HSV)

fig, axes = plt.subplots(1, 3, figsize=(15, 5))
axes[0].imshow(img_rgb)
axes[0].set_title('Original')
<strong>axes[1].imshow(gray, cmap='gray')
</strong>axes[1].set_title('Grayscale')
axes[2].imshow(cv2.cvtColor(hsv, cv2.COLOR_HSV2RGB))
axes[2].set_title('HSV visualised')
for ax in axes: ax.axis('off')
plt.show()

# Print HSV value of the centre pixel
h, w = img_bgr.shape[:2]
print(f'Centre pixel HSV: {hsv[h//2, w//2]}')
</code></pre>

***

**Q. Print the HSV value of a pixel you know is on the pitch. What is the Hue value? Is it in the 35–85 range?**

### Define bounds and create mask

```python
# Define HSV range for grass green
lower_green = np.array([35, 50, 50])   # [Hue_min, Sat_min, Val_min]
upper_green = np.array([85, 255, 255]) # [Hue_max, Sat_max, Val_max]

# Create mask — white where colour is in range, black elsewhere
mask = cv2.inRange(hsv, lower_green, upper_green)

print(f'Mask shape: {mask.shape}')
print(f'Pitch pixels detected: {np.sum(mask > 0)}')

plt.figure(figsize=(8, 5))
plt.imshow(mask, cmap='gray')
plt.title('Green mask (white = pitch pixels)')
plt.axis('off')
plt.show()

```

***

**Q. Does the white area match the pitch? Are players' shirts being incorrectly included? Try adjusting the Saturation minimum to fix it.**

### Apply mask and display result

```python
# Apply mask to original image — keep only pitch pixels
pitch_only = cv2.bitwise_and(img_bgr, img_bgr, mask=mask)
pitch_rgb  = cv2.cvtColor(pitch_only, cv2.COLOR_BGR2RGB)

fig, axes = plt.subplots(1, 3, figsize=(18, 5))
axes[0].imshow(img_rgb)
axes[0].set_title('Original')
axes[1].imshow(mask, cmap='gray')
axes[1].set_title('Mask')
axes[2].imshow(pitch_rgb)
axes[2].set_title('Pitch isolated')
for ax in axes: ax.axis('off')
plt.tight_layout()
plt.show()

# Calculate pitch coverage percentage
total_pixels  = mask.shape[0] * mask.shape[1]
pitch_pixels  = np.sum(mask > 0)
coverage      = (pitch_pixels / total_pixels) * 100
print(f'Pitch covers {coverage:.1f}% of the image')
```

***

**Q: What percentage of the image is pitch? Does the isolated result look clean? What is still leaking through the mask?**

### Extension: isolate a kit colour

```python
# EXTENSION - Try isolating a player's kit
# Change these bounds to match a shirt colour in your image
lower_kit = np.array([100, 120, 70])   # Blue kit example
upper_kit = np.array([130, 255, 255])

kit_mask  = cv2.inRange(hsv, lower_kit, upper_kit)
kit_only  = cv2.bitwise_and(img_bgr, img_bgr, mask=kit_mask)
kit_rgb   = cv2.cvtColor(kit_only, cv2.COLOR_BGR2RGB)

fig, axes = plt.subplots(1, 2, figsize=(12, 5))
axes[0].imshow(kit_mask, cmap='gray')
axes[0].set_title('Kit mask')
axes[1].imshow(kit_rgb)
axes[1].set_title('Kit isolated')
for ax in axes: ax.axis('off')
plt.show()

```
