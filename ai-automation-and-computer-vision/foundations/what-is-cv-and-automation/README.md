---
description: Produce a labelled pipeline diagram for a football offside detector
icon: head-side-gear
cover: ../../../.gitbook/assets/1_NEp8ozjqtzhDjVNPB4jxGg.png
coverY: 0
---

# What is CV and Automation?

### Learning Objectives

1. Define computer vision (CV) and explain what distinguishes it from regular programming
2. Describe the 3-stage automation pipeline: Perceive → Decide → Act
3. Identify at least 5 real-world CV applications across sports, industry, and retail
4. Map a complete CV automation pipeline for a specific sports use case (football offside detector)
5. Explain in plain English what a CV system 'sees' and how it 'decides' what to do next



### What is Computer Vision?

Computer vision (CV) is a field of artificial intelligence that enables computers to interpret and understand visual information from the world images, video, and live camera feeds. Where traditional programming tells a computer exactly what to do with data, CV teaches a computer to extract meaning from pixels.

#### What a CV system can do

* Detect and classify objects  (e.g. ball, player, goal, referee)
* Track movement across frames  (e.g. follow a player's position every 30ms)
* Measure and count  (e.g. how many players are in the penalty box?)
* Trigger decisions  (e.g. alert when the ball crosses the goal line)
* Generate descriptions  (e.g. 'Player 7 is offside')

### The Automation Pipeline: Perceive → Decide → Act

Every CV automation system from a self-driving car to a football analytics tool follows the same three-stage pipeline. Understanding this pipeline is the foundation of everything we build in this course.

<table><thead><tr><th align="center">Stage</th><th width="223">What happens</th><th>Football offside example</th><th valign="middle">Tech involved</th></tr></thead><tbody><tr><td align="center"><strong>PERCEIVE</strong></td><td>Camera captures input. CV model processes each frame to identify objects</td><td>Webcam captures the pitch. YOLOv8 detects every player and the ball, drawing bounding boxes in real time.</td><td valign="middle"><em>Webcam, OpenCV, YOLOv8</em></td></tr><tr><td align="center"><strong>DECIDE</strong></td><td>The system applies rules or a model to interpret what it perceives.</td><td>Code checks: is any attacker's position beyond the last defender when the ball is played?</td><td valign="middle">Python logic, spatial coordinates, rule engine</td></tr><tr><td align="center"><strong>ACT</strong></td><td>The system triggers a response based on its decision.</td><td>If offside: draw a red line on screen, log the event, play an alert sound.</td><td valign="middle">OpenCV drawing, event log, audio trigger</td></tr></tbody></table>
