
# Task 1: Custom Object Detection from Scratch (Faster R-CNN)

## Overview

This project implements a complete **object detection pipeline trained entirely from scratch** (no pretrained weights) using a **Faster R-CNN architecture with a custom CNN backbone**. The objective was to design, train, evaluate, and analyze an object detection model on a custom dataset, and to evaluate it based on **accuracy (mAP)**, **inference speed (FPS)**, and **model size**, along with a discussion of accuracy–speed trade-offs.
The implementation was carried out using **PyTorch** on **Google Colab GPU**.

---

## Dataset

* **Source**: Kaggle – Vehicle Detection (8 classes, YOLO format)
* **Classes used (subset)**:

  * Car
  * Bus
  * Truck
  * Motorcycle
  * Bicycle
* **Reason for class subset**:

  * Improves stability when training from scratch
  * Reduces class imbalance
  * Faster convergence under limited compute

### Annotation Format

* Original annotations were in **YOLO format**:

  ```
  class_id x_center y_center width height (normalized)
  ```
* Converted to **Faster R-CNN format**:

  ```
  [xmin, ymin, xmax, ymax] (absolute pixel coordinates)
  ```

### Dataset Handling

* Images without valid bounding boxes (after class filtering) were skipped
* Dataset size limited to **2000 images** for computational feasibility

---

## Architecture Design

### Model Choice: Faster R-CNN

Faster R-CNN was selected because:

* It is a **two-stage detector** optimized for accuracy
* Explicit **Region Proposal Network (RPN)** allows better localization
* Suitable for applications where precision is prioritized over real-time speed

### Custom CNN Backbone (From Scratch)

No pretrained backbones (ResNet, MobileNet, etc.) were used.

**Backbone Architecture**:

* 4 convolutional blocks
* Progressive channel expansion: 3 → 32 → 64 → 128 → 256
* Batch Normalization + ReLU activations
* One MaxPooling layer for spatial reduction

**Design Rationale**:

* Lightweight backbone to reduce memory usage
* Shallow depth to ensure stable training from scratch
* Sufficient feature abstraction for vehicle detection

---

## Data Augmentation Strategy

Given the absence of pretrained weights, preventing overfitting was critical.

**Applied Strategies**:

* Implicit scale variation via image resizing
* Dataset shuffling
* Skipping empty or invalid samples

**Not applied deliberately**:

* Heavy geometric augmentations (to avoid destabilizing from-scratch training)
* Complex color distortions

The focus was on **stable convergence** rather than aggressive augmentation.

---

## Training Methodology

### Training Setup

* Framework: PyTorch
* Device: Google Colab GPU
* Optimizer: Adam
* Learning Rate: 1e-3
* Batch Size: 2 (memory-safe)
* Epochs: 3
* Mixed Precision Training (AMP): Enabled

### Key Training Optimizations

* Mixed precision (FP16) to reduce GPU memory usage
* Reduced RPN proposals:

  * Pre-NMS: 600
  * Post-NMS: 300
* Image resizing (max dimension = 800 px)
* Skipping empty batches and invalid samples

### Loss Function

* Faster R-CNN composite loss:

  * RPN classification loss
  * RPN bounding box regression loss
  * ROI classification loss
  * ROI bounding box regression loss

---

## Evaluation Methodology

The model was evaluated on three primary metrics:

### 1. Accuracy — mAP@0.5

* Approximate mean Average Precision at IoU ≥ 0.5
* Computed on a representative subset
* Samples without ground-truth boxes were skipped

**Result**:

* **mAP@0.5 = 0.769**

### 2. Inference Speed — FPS

* Measured using GPU inference
* Forward-pass only (no gradient computation)
* Warm-up iterations applied

**Result**:

* **Inference Speed = 9.92 FPS**

### 3. Model Size

* Saved PyTorch checkpoint (`.pth`)

**Result**:

* **Model Size = 57 MB**

---

## Results Summary

| Metric          | Value    |
| --------------- | -------- |
| mAP@0.5         | 0.769    |
| Inference Speed | 9.92 FPS |
| Model Size      | 57 MB    |

---

## Accuracy vs Speed Trade-off Discussion

Faster R-CNN inherently prioritizes **accuracy over speed** due to its two-stage design. The achieved results highlight the expected trade-offs:

* High detection accuracy was achieved despite training from scratch
* Inference speed is lower compared to one-stage detectors (e.g., YOLO), but still practical
* Reduced RPN proposals and mixed precision significantly improved FPS without major accuracy loss
* Model size remains moderate, making deployment feasible for accuracy-critical applications

This makes the model suitable for scenarios such as:

* Surveillance
* Traffic analysis
* Industrial inspection
  where precision is more important than ultra-low latency.

---

## Demo and Visual Results

A short demo video demonstrating real-time inference is included in the repository.
The video shows bounding boxes, confidence scores, and real detection results on unseen images.

---

## Conclusion

This project demonstrates a complete object detection pipeline built **entirely from scratch**, covering:

* Dataset preparation and annotation conversion
* Custom CNN backbone design
* Faster R-CNN training and optimization
* Comprehensive evaluation and analysis

Despite limited compute and training time, the model achieved strong performance, validating the effectiveness of the architectural and training choices.

---

## Future Improvements

* Full COCO-style mAP evaluation
* Additional data augmentation
* Model pruning or quantization
* Comparison with one-stage detectors (YOLO) for speed benchmarking

