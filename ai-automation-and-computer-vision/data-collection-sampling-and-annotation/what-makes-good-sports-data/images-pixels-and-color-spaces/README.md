---
description: Isolate the green pitch from a football image using HSV hue masking in Python
icon: camera
cover: ../../../../.gitbook/assets/shutterstock-1999833305-low-720x480.jpg
coverY: 0
---

# Images, Pixels & Color Spaces

### Learning Objectives

1. Explain what a pixel is and how an image is represented as a NumPy array
2. Describe the RGB color model and access individual channel values
3. Convert an image between RGB, BGR, grayscale, and HSV color spaces using OpenCV
4. Explain why HSV is more useful than RGB for color-based automation decisions
5. Write a Python function that isolates a specific color range using cv2.inRange()
6. Apply a hue mask to isolate the green football pitch from a real image



### What is a Pixel?

A pixel (picture element) is the smallest unit of a digital image. Every image is a grid of pixels, and every pixel is just a set of numbers. A colour image has three numbers per pixel one for each colour channel.

{% hint style="info" %}
Key Fact :&#x20;

* A 640×480 webcam frame = 640 columns × 480 rows = 307,200 pixels.
* Each pixel has 3 values (Blue, Green, Red in OpenCV) from 0–255.
* Total numbers in one frame: 307,200 × 3 = 921,600 values.
* That is what a CV model processes every single frame at 30 frames per second.

**Now calculate what your eyes do every single second and how fast your brain processes when you see things. Remember that your vision is just not 640 x 480px. Find out the resolution of a human eye.**
{% endhint %}

In Python + NumPy, an image is a 3D array with shape (height, width, channels)

```python
import cv2, numpy as np

img = cv2.imread('football_pitch.jpg')
print(img.shape)         # → (480, 640, 3)  — height, width, channels
print(img.dtype)         # → uint8  — values 0–255

# Access one pixel at row 240, column 320 (centre of frame)
pixel = img[240, 320]          # → [B, G, R]  e.g. [34, 142, 28]

```

***

### Color Models : RGB, BGR, Grayscale, HSV

Different color models represent color in different ways. OpenCV uses BGR (not RGB) by default this is one of the most common beginner mistakes. Understanding all four models is essential for CV automation.

<table data-header-hidden><thead><tr><th width="105.4000244140625" valign="top">Model</th><th width="233.79998779296875" valign="top">What it stores</th><th width="192.5999755859375" valign="top">Best used for</th><th valign="top">OpenCV conversion</th></tr></thead><tbody><tr><td valign="top">RGB / BGR</td><td valign="top">3 channels: Red, Green, Blue (0–255 each). OpenCV reads as BGR - Blue first.</td><td valign="top">Display, basic image ops. Not ideal for color filtering.</td><td valign="top">cv2.cvtColor(img, cv2.COLOR_BGR2RGB)</td></tr><tr><td valign="top">Grayscale</td><td valign="top">1 channel: brightness only (0=black, 255=white). No color info.</td><td valign="top">Edge detection, shape analysis, motion detection.</td><td valign="top">cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)</td></tr><tr><td valign="top">HSV</td><td valign="top">3 channels: Hue (color type 0–179), Saturation (intensity 0–255), Value (brightness 0–255).</td><td valign="top">Color-based filtering and masking. Best for isolating the pitch.</td><td valign="top">cv2.cvtColor(img, cv2.COLOR_BGR2HSV)</td></tr><tr><td valign="top">LAB</td><td valign="top">3 channels: Lightness, A (green–red), B (blue–yellow). Perceptually uniform.</td><td valign="top">Colour correction, skin detection, advanced filtering.</td><td valign="top">cv2.cvtColor(img, cv2.COLOR_BGR2LAB)</td></tr></tbody></table>

***

### Why HSV Beats RGB for Automation Decisions?

RGB mixes color and brightness together. If the light changes, the RGB values of the green pitch change drastically making it very hard to write a reliable rule. HSV separates Hue (what color) from Value (how bright). This means you can define green pitch once using the Hue range, and it will work across different lighting conditions.

&#x20;

<table data-header-hidden><thead><tr><th valign="top">Color</th><th width="187" valign="top">Visual</th><th valign="top">RGB (B,G,R)</th><th valign="top">HSV (H,S,V)</th></tr></thead><tbody><tr><td valign="top">Pitch green (sunny)</td><td valign="top"><p></p><p><mark style="color:$success;background-color:green;"><strong>Bright grass green</strong></mark></p></td><td valign="top">(34, 142, 28)</td><td valign="top">(58, 196, 142)</td></tr><tr><td valign="top">Pitch green (shade)</td><td valign="top"><mark style="background-color:$success;">Darker grass area</mark></td><td valign="top">(18, 82, 14)</td><td valign="top">(56, 188, 82)</td></tr><tr><td valign="top">Pitch green (night)</td><td valign="top"><mark style="color:green;background-color:green;">Floodlit green</mark></td><td valign="top">(22, 100, 20)</td><td valign="top">(57, 180, 100)</td></tr><tr><td valign="top"><strong>What changes?</strong></td><td valign="top"><strong>All three conditions</strong></td><td valign="top"><strong>All 3 values change significantly - hard to write one rule (imagine coding this... )</strong></td><td valign="top"><strong>Hue stays ~55–60 - one rule works across all conditions (Hence we can write better coding logics)</strong></td></tr></tbody></table>

***

### The HSV Hue Wheel - Finding Pitch Green

In OpenCV, Hue runs from 0 to 179 (not 0–360 as in standard HSV - OpenCV halves it to fit in a uint8). Key reference ranges:

<table data-header-hidden><thead><tr><th width="123.79998779296875" valign="top">Color</th><th width="158.39996337890625" valign="top">Hue range (OpenCV)</th><th width="228.800048828125" valign="top">Sports use case</th><th valign="top">lower / upper bound (H,S,V)</th></tr></thead><tbody><tr><td valign="top">Red (kit)</td><td valign="top">0–10 and 160–179</td><td valign="top">Red team shirt detection</td><td valign="top">(0,120,70) / (10,255,255)</td></tr><tr><td valign="top">Orange (ball)</td><td valign="top">10–25</td><td valign="top">Orange football, traffic cones</td><td valign="top">(10,150,100) / (25,255,255)</td></tr><tr><td valign="top">Yellow (kit)</td><td valign="top">25–35</td><td valign="top">Yellow jersey, yellow card</td><td valign="top">(25,150,100) / (35,255,255)</td></tr><tr><td valign="top">Green (pitch)</td><td valign="top">35–85</td><td valign="top">Football pitch isolation  ★</td><td valign="top">(35,50,50) / (85,255,255)</td></tr><tr><td valign="top">Blue (sky/kit)</td><td valign="top">100–130</td><td valign="top">Blue team shirt, sky background</td><td valign="top">(100,120,70) / (130,255,255)</td></tr><tr><td valign="top">White (lines)</td><td valign="top">0–179, S&#x3C;30</td><td valign="top">Pitch line detection</td><td valign="top">(0,0,220) / (179,30,255)</td></tr></tbody></table>
