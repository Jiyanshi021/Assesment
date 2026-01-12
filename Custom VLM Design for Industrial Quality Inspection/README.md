# Custom Visual–Language Model (VLM) Design for Industrial Quality Inspection

## Problem Statement

A semiconductor manufacturing facility requires an **offline AI-based inspection system** for **Printed Circuit Board (PCB) quality control**. Inspectors should be able to ask **natural language questions** about defects, and the system must respond with **accurate, structured outputs** including defect locations and confidence scores.

The system must satisfy the following constraints:

* Operate **offline**
* Respond within **< 2 seconds**
* Use **50,000 PCB images with defect bounding boxes**
* Handle **no existing question–answer (QA) pairs**
* Minimize hallucinations common in generic VLMs

---

## Objectives

* Design a **custom VLM architecture** tailored for PCB defect inspection
* Ensure **precise localization and counting**
* Prevent hallucinated responses
* Optimize for **fast inference and industrial deployment**

---

## (A) Model Selection

### Chosen Approach: **Custom Detector-Grounded VLM**

Instead of relying on a generic VLM, a **custom modular architecture** is proposed, combining:

* A **PCB defect detection model** (vision backbone)
* A **lightweight instruction-tuned language model**
* A **structured fusion mechanism**

### Rationale for Rejecting Generic VLMs

| Model        | Limitation                                               |
| ------------ | -------------------------------------------------------- |
| LLaVA        | High hallucination, weak spatial grounding               |
| BLIP-2       | Caption-centric, poor localization                       |
| Qwen-VL      | Large size, slower inference                             |
| Generic VLMs | Free-form text generation unsuitable for precision tasks |

### Key Selection Factors

* Deterministic outputs
* Precise spatial grounding
* Inference latency under 2 seconds
* Fine-tuning flexibility
* Offline deployability
* Industry-friendly licensing

---

## (B) Architecture Design Strategy

### High-Level Architecture

```
PCB Image
   ↓
Defect Detection Model (Vision Encoder)
   ↓
Structured Defect Tokens
   ↓
Fusion Layer
   ↓
Language Decoder
   ↓
Structured Response
```

---

### Vision Encoder (Detector)

* Trained on PCB images with bounding boxes
* Outputs:

  * Defect type
  * Bounding box coordinates
  * Confidence score
* No image captioning or free-form visual tokens

**Example Detector Output**

```json
{
  "defect_type": "missing_component",
  "bbox": [x1, y1, x2, y2],
  "confidence": 0.93
}
```

---

### Language Decoder

* Lightweight LLM (1–3B parameters)
* Instruction-tuned
* Receives **only structured defect tokens**
* Generates structured, fact-based answers

---

### Fusion Mechanism (Critical Design Choice)

* Replaces raw image-to-text attention
* Converts defect outputs into tokens:

  ```
  <DEFECT type=missing_component x=120 y=85 conf=0.93>
  ```
* Ensures all responses are grounded in detected evidence

---

## (C) Optimization for < 2s Inference (Offline)

### Techniques Applied

1. **Model Size Constraints**

   * Vision model ≤ 30M parameters
   * Language model ≤ 3B parameters

2. **Quantization**

   * INT8 / INT4 inference
   * Post-training quantization

3. **Pruning**

   * Channel pruning in vision backbone
   * Removal of unused attention heads

4. **LoRA Fine-Tuning**

   * Parameter-efficient tuning of language model
   * No full model retraining required

5. **Pipeline Execution**

   * Vision model executes first
   * Language model processes compact defect tokens

---

## (D) Hallucination Mitigation Strategy

### Core Principle

**The language model is constrained to reason only over detected facts.**

### Techniques Used

* Detector-first architecture (no detection → no claims)
* Structured output enforcement (JSON-like responses)
* Negative sampling with defect-free images
* Penalizing references to non-existent defects
* Confidence-aware uncertainty handling

---

## (E) Training Plan

### Stage 1: Vision Model Training

* Train detector on 50,000 PCB images
* Metrics:

  * mAP@0.5
  * Localization IoU

---

### Stage 2: QA Pair Generation

Since no QA pairs exist, they are **synthetically generated** from annotations:

* Questions about defect count, location, and type
* Answers derived deterministically from bounding boxes

---

### Stage 3: Language Model Fine-Tuning

* LoRA-based instruction tuning
* Inputs:

  * Defect tokens
  * Natural language questions
* Outputs:

  * Structured responses only

---

### Stage 4: End-to-End Validation

* Vision and language models evaluated jointly
* No gradient flow between components for stability

---

## (F) Validation and Evaluation

### Localization Accuracy

* Intersection over Union (IoU)
* Target: IoU ≥ 0.5

### Counting Accuracy

* Mean Absolute Error (MAE)
* Ground truth vs predicted defect count

### Hallucination Rate

* Percentage of incorrect claims on defect-free images
* Target: < 2%

### Inference Latency

* End-to-end timing (image + query)
* Target: < 2 seconds

---

## Expected Outcomes

* Precise defect localization and classification
* Accurate responses to natural language queries
* Minimal hallucination due to detector grounding
* Fast and reliable offline inference
* Scalable to additional defect categories

---

## Conclusion

Generic VLMs are unsuitable for high-precision industrial inspection due to hallucinations and weak spatial grounding. This project proposes a **custom, detector-grounded VLM architecture** that ensures accuracy, reliability, and real-time performance for PCB quality inspection. The modular design makes the system robust, interpretable, and suitable for real-world manufacturing deployment.

---

## Future Enhancements

* Segmentation-based defect localization
* Active learning for rare defect discovery
* Integration with factory automation systems
* Real-time camera-based inspection

---

### Jiyanshi Batra

**[Your Name]**

