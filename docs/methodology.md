# Methodology

This document describes the methodology used in the current stage of the region-specific ground-based drone detection project.

The project follows a baseline-first workflow: train a YOLO11 model on a public drone detection dataset, evaluate it on the public dataset, and then test the same model on locally collected drone videos to observe public-to-local transfer behavior.

---

## 1. Research Design

The project was designed to investigate whether a public-dataset-trained drone detector performs reliably in local ground-based video conditions.

The current stage does not use local data for training. Instead, local drone videos are used only for inference and qualitative evaluation. This makes the local result useful for studying domain shift.

The workflow is:

```text
Public drone dataset
        ↓
YOLO11 baseline training
        ↓
Public dataset quantitative evaluation
        ↓
Local video inference
        ↓
Qualitative local error analysis
        ↓
Future local annotation evaluation and fine-tuning
```

---

## 2. Objective of the Current Stage

The objective of the current stage is to establish a strong public-trained baseline and observe how it behaves on local drone footage.

Specific objectives:

1. Train a YOLO11 object detection model using a public drone detection dataset.
2. Evaluate the trained model on the public dataset using object detection metrics.
3. Test the trained model on locally collected drone video clips.
4. Identify local false positives, missed detections, and unstable detections.
5. Determine whether a public-to-local performance gap exists.
6. Prepare the foundation for future local fine-tuning.

---

## 3. Public Dataset Preparation

The public drone dataset was prepared in YOLO object detection format.

The dataset structure follows:

```text
public_drone_dataset/
│
├── images/
│   ├── train/
│   ├── val/
│   └── test/
│
├── labels/
│   ├── train/
│   ├── val/
│   └── test/
│
└── data.yaml
```

The detection task uses one class:

```text
0: drone
```

The `data.yaml` file defines the training, validation, and test paths.

Example:

```yaml
path: /path/to/public_drone_dataset
train: images/train
val: images/val
test: images/test

names:
  0: drone
```

The public dataset was the only training data source in the current stage.

---

## 4. Local Video Collection

The local dataset consists of 11 short drone video clips collected from a ground-based phone-camera perspective.

The local clips include different visual conditions:

* Day
* Evening
* Night
* Night with heavy background lighting
* Single-drone scenes
* Multi-drone scenes
* Clear-sky scenes
* Background-clutter scenes
* Human-present scenes
* Buildings
* Windows
* Trees
* Branches
* Poles

The local videos were not used during training. They were used only to test how the public-trained model behaves in local environments.

---

## 5. Annotation Process

The local videos were annotated using bounding boxes around visible drone objects.

The annotations were exported in COCO-style format, where bounding boxes are represented as:

```text
[x, y, width, height]
```

These annotations are prepared for future formal evaluation. However, the local annotation evaluation pipeline was not completed in the current stage.

### Future annotation conversion

For formal YOLO evaluation, the COCO annotations need to be converted to YOLO format:

```text
[class_id, x_center, y_center, width, height]
```

The values must be normalized by image width and image height.

After conversion, the local dataset can be evaluated using the YOLO validation pipeline.

---

## 6. Model Selection

YOLO11 was selected as the object detection model.

YOLO-family models are suitable for this project because they are:

* Fast
* Practical for video inference
* Easy to train on custom datasets
* Commonly used for real-time object detection
* Supported by the Ultralytics training workflow

The current model is trained for single-class drone detection.

---

## 7. Training Setup

The YOLO11 model was trained using the public drone dataset.

Training configuration summary:

| Parameter                   | Value / Description            |
| --------------------------- | ------------------------------ |
| Model family                | YOLO11                         |
| Task                        | Object detection               |
| Class count                 | 1                              |
| Class name                  | drone                          |
| Training data               | Public drone detection dataset |
| Local data used in training | No                             |
| Evaluation data             | Public validation/test split   |
| Local evaluation            | Qualitative video inference    |

A typical training command is:

```bash
python scripts/train_yolo11_baseline.py \
  --data data/public_drone_dataset/data.yaml \
  --model yolo11n.pt \
  --epochs 100 \
  --imgsz 640
```

---

## 8. Public Dataset Evaluation

After training, the model was evaluated on the public dataset.

The public evaluation used standard object detection metrics:

* Precision
* Recall
* mAP@0.5
* mAP@0.5:0.95

The trained YOLO11 baseline achieved:

| Metric       |  Value |
| ------------ | -----: |
| Precision    | 0.9663 |
| Recall       | 0.9498 |
| mAP@0.5      | 0.9840 |
| mAP@0.5:0.95 | 0.7210 |

These results show that the model learned the public benchmark drone detection task effectively.

---

## 9. Local Video Inference

After public dataset evaluation, the trained model was applied directly to the 11 local drone videos.

The purpose was to observe:

* Whether drones were detected
* Where detections failed
* Which objects caused false positives
* Whether detection was stable across frames
* Whether multiple drones were detected correctly
* How confidence behaved across clips

A typical local video inference command is:

```bash
python scripts/run_local_video_inference.py \
  --model runs/detect/train/weights/best.pt \
  --source data/local_videos/
```

The local result was evaluated qualitatively by inspecting predicted videos and confidence summaries.

---

## 10. Evaluation Metrics

### Precision

Precision measures how many predicted drone detections are correct.

```text
Precision = True Positives / (True Positives + False Positives)
```

High precision means fewer false alarms.

### Recall

Recall measures how many actual drones are detected.

```text
Recall = True Positives / (True Positives + False Negatives)
```

High recall means fewer missed drones.

### mAP@0.5

mAP@0.5 measures mean average precision at an IoU threshold of 0.5.

### mAP@0.5:0.95

mAP@0.5:0.95 is stricter because it averages mAP across multiple IoU thresholds from 0.5 to 0.95.

### Confidence

Confidence represents how confident the model is in its prediction. In the local video analysis, confidence was used only as a diagnostic signal.

Confidence is not the same as accuracy.

A high-confidence detection can still be wrong if the model predicts a drone on a human, window, tree branch, or pole.

---

## 11. Public-to-Local Transfer Evaluation

The public-trained model performed strongly on the public dataset but showed unstable behavior in local videos.

The local evaluation showed:

* Good detection when the drone appeared against a clear sky
* False positives on local background objects
* Missed detections when drones were far away
* Missed detections in multi-drone scenes
* False positives on humans, windows, branches, poles, and building structures
* Lower reliability in cluttered backgrounds

This indicates a public-to-local domain shift.

---

## 12. Current Limitations

The current methodology has several limitations.

1. The local result is qualitative, not formal mAP-based evaluation.
2. Local annotations were prepared but not fully converted into YOLO evaluation format.
3. The local dataset is small and contains only 11 clips.
4. No local fine-tuning was performed in the current stage.
5. Hard negative samples were not systematically included in training.
6. Real-time deployment was not tested.
7. Confidence summaries were used only for diagnosis, not as proof of accuracy.

---

## 13. Future Methodological Improvements

The next methodological improvements should include:

1. Convert local annotations from COCO to YOLO format.
2. Build a verified local YOLO test dataset.
3. Run formal local validation.
4. Fine-tune the public-trained model using local footage.
5. Add hard negative local samples.
6. Compare public-only, local-only, and public-plus-local training.
7. Evaluate performance by scene condition.
8. Add tracking for video stability.
9. Test real-time deployment.

---

## 14. Summary

The methodology establishes a clean public-trained YOLO11 baseline and tests its transfer behavior on local drone videos.

The public dataset evaluation shows that the model trained successfully. The local qualitative evaluation shows that benchmark performance does not fully transfer to local conditions.

This baseline-first methodology provides the foundation for the next research stage: formal local evaluation and region-specific fine-tuning.

