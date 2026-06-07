# PE Contamination Detection — Prometeon TTM902

Real-time computer vision pipeline for detecting polyethylene (PE) film 
contamination on tire manufacturing machines at Prometeon's Milan plant.

## The Problem
PE film left over from earlier production stages can pass through the TTM902 
machine and cause compound defects and product rejection. Manual inspection 
is unreliable and slow.

## Solution Architecture

A multi-stage YOLOv11 pipeline running on live camera feed:

**Stage 0 — Obstruction Guard**  
Pretrained COCO person detector (conf ≥ 0.45) flags frames where an operator 
is in view and skips them to avoid false positives.

**Stage 1 — Compound Segmentation**  
YOLOv11-seg model isolates the compound region and normalizes it into a 
640×640 crop, removing background noise.

**Stage 2 — PE Classification**  
YOLOv11-cls binary classifier (conf ≥ 0.70) runs on the clean crop.  
A 5-frame majority-vote buffer smooths transient predictions.

**Stage 3 — Real-time Dashboard**  
Results are streamed via WebSocket to a browser dashboard showing:
- Live annotated camera feed
- Total defect count (rising-edge logic — one count per clean→PE transition)
- Per-defect timestamp log with confidence scores
- Shift progress tracker
- CSV export

## Why Not Direct Detection?
PE film is translucent and shapeless — direct object detection failed.  
HSV color contrast analysis also failed under the plant's variable lighting.  
The segmentation + classification approach was the reasoned solution to 
these constraints.

## Tech Stack
- Python, OpenCV
- YOLOv11 (segmentation + classification)
- WebSocket (`websockets` library)
- Vanilla HTML/CSS/JS dashboard
- Windows, USB camera (RTSP-ready)

## Demo
<img width="600" height="337" alt="image" src="https://github.com/user-attachments/assets/e92d6348-3e18-4e52-9c69-22246d4c5ac8" />
<img width="1456" height="819" alt="image" src="https://github.com/user-attachments/assets/7aa8443c-fc45-4c20-9f92-7f69604e3c78" />
<img width="1456" height="819" alt="image" src="https://github.com/user-attachments/assets/55570202-bd4e-4c61-8fc1-848acceb05ac" />

