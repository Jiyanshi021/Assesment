# Automated Quality Inspection System for Manufacturing

## Overview

This project implements an **automated visual quality inspection system** for manufacturing environments using computer vision. The system analyzes images of manufactured items to **detect, localize, and classify defects** before packaging, reducing reliance on manual inspection and improving quality assurance.

The solution demonstrates a complete inspection pipeline including **defect detection, localization, classification, confidence scoring, defect center extraction, and severity assessment**.

---

## Manufactured Item Chosen

**Automotive body / vehicle component**

Automotive components are widely used in manufacturing quality inspection due to their visible surface features and susceptibility to defects such as scratches, dents, misalignment, and discoloration.

This choice satisfies the requirement of a manufactured item with clearly visible features suitable for automated inspection.

---

## Defect Types Considered

The system is designed to identify and analyze the following defect categories:

1. **Surface Damage (Scratches / Dents)**
2. **Structural Deformation / Misalignment**
3. **Visual Anomalies (Discoloration, Burn Marks)**

In addition, **defect-free samples** are included to demonstrate normal inspection behavior where no defects are detected.

---

## Data Source

* Real-world images sourced from publicly available datasets and online resources
* Includes:

  * Defective manufactured items
  * Non-defective (normal) samples
* Images are stored in the repository under structured directories

---

## System Architecture

### Model

* **YOLOv8 (COCO-pretrained)** object detection model
* Used to localize visually salient regions on manufactured items

Although the model is pretrained on general objects, it effectively highlights **visually anomalous regions**, which are treated as defect candidates in an inspection pipeline.

---

## Inspection Pipeline

The system follows the steps below:

1. **Input Image Analysis**

   * Reads the input image of the manufactured item

2. **Defect Detection & Localization**

   * Detects regions of interest using bounding boxes

3. **Defect Classification**

   * Assigns a defect category based on detected object/region
   * Outputs a confidence score for each detected defect

4. **Defect Center & Severity Assessment**

   * Computes the (x, y) pixel coordinates of defect centers
   * Estimates defect severity based on defect area relative to image size

---

## Severity Assessment Strategy

Severity is calculated using a simple, interpretable rule:

* **Low Severity** – Small defect area
* **Medium Severity** – Moderate defect area
* **High Severity** – Large or critical defect region

This approach reflects common practices in industrial quality inspection systems.

---

## Output Format

For each detected defect, the system outputs structured data in JSON format:

```json
{
  "defect_type": "surface_damage",
  "confidence": 0.81,
  "bounding_box": [x1, y1, x2, y2],
  "center": [cx, cy],
  "severity": "high"
}
```

Additionally, annotated images with bounding boxes and defect labels are saved for visual verification.

---

## Repository Structure

```
task2_quality_inspection/
├── images/
│   ├── defective/
│   └── non_defective/
├── output/
│   ├── annotated_image.jpg
│   └── output.json
├── inspect_quality.py
└── README.md
```

---

## Results

* Successfully detects and localizes defect regions
* Outputs confidence scores and defect centers
* Differentiates between defective and non-defective samples
* Provides severity assessment for decision support

---

## Limitations

* The pretrained model is not specifically trained on manufacturing defects
* Subtle defects may not always be detected
* Domain-specific fine-tuning could further improve accuracy

---

## Conclusion

This project demonstrates a practical **automated quality inspection system** suitable for manufacturing environments. The pipeline showcases how computer vision can be applied to detect and analyze defects before packaging, improving efficiency and consistency in quality control processes.

---

## Future Improvements

* Fine-tuning the model on manufacturing-specific defect datasets
* Incorporating segmentation-based defect localization
* Real-time inspection using live camera feeds
* Integration with industrial automation systems

