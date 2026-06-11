# Region-Specific Ground-Based Drone Detection Using YOLO11

## Public Dataset Baseline and Local Video Transfer Evaluation

This repository contains an ongoing computer vision research project on **ground-based drone detection using YOLO11**. The project investigates whether a drone detection model trained on a public drone dataset transfers reliably to locally collected drone videos.

The current stage of the project establishes a **public-dataset-trained YOLO11 baseline** and evaluates its behavior on **11 locally collected drone video clips**. The results show that strong public dataset performance does not automatically guarantee stable local performance because of domain shift, local background clutter, lighting variation, and hard negative objects.

---

## Project Status

This project is currently in the **baseline and local transfer evaluation stage**.

### Completed

* Public drone dataset preparation
* YOLO11 baseline training
* Public dataset quantitative evaluation
* Local video inference on 11 collected clips
* Qualitative local error analysis
* Confidence summary for local video predictions
* Identification of public-to-local domain shift issues

### Ongoing / Future Work

* Convert local COCO annotations into YOLO format
* Create a formal local YOLO test dataset
* Evaluate the public-trained model on local annotations using precision, recall, and mAP
* Fine-tune the public-trained model using local region-specific drone data
* Compare:

  * Public-only training
  * Local-only training
  * Public + local fine-tuning
* Add hard negative samples such as birds, windows, poles, tree branches, humans, empty sky, rooftops, and night lights
* Test real-time inference and edge deployment

---

## Research Question

The main research question is:

> Does a drone detection model trained on a general public drone dataset perform reliably in a specific local region, or does region-specific data become necessary for practical deployment?

The current project does not yet claim that a locally trained model performs better. Instead, it establishes the first step: measuring how a public-trained YOLO11 model behaves when tested on local drone footage.

---

## Project Summary

Drone detection using ordinary ground-based cameras is a challenging object detection problem. Drones are often small, fast-moving, and visually similar to objects such as birds, kites, tree branches, building edges, windows, poles, distant lights, or rooftop structures.

Public drone datasets are useful for training baseline models. However, public datasets may not represent the exact visual conditions of a target deployment region. A public dataset may contain different lighting, drone sizes, camera viewpoints, weather, backgrounds, and recording conditions compared with local ground-camera footage.

This project trains a YOLO11 detector on a public ground-based drone detection dataset and applies the trained model directly to local drone videos. The goal is to identify whether a public-to-local performance gap exists before attempting region-specific fine-tuning.

---

## Why This Project Matters

A drone detector that performs well on a public benchmark may still fail in a real local environment.

In real deployment, the model must handle:

* Small drones far from the camera
* Drones moving across complex backgrounds
* Day, evening, and night lighting
* Building edges and small windows
* Tree branches and poles
* Humans in the scene
* Multiple drones in one video
* Heavy background lighting and glare
* False alarms caused by local objects

For practical use, detection performance should not only be measured by public dataset mAP. It should also be tested under local deployment conditions.

This project focuses on that gap.

---

## Key Contributions

* Trained a YOLO11 object detection model for single-class drone detection.
* Evaluated the trained model quantitatively on the public drone dataset.
* Tested the public-trained model on 11 locally collected drone video clips.
* Identified local false-positive sources such as:

  * Building windows
  * Building tops
  * Tree branches
  * Poles
  * Humans
  * Background lighting
* Documented missed detections in far-range, cluttered-background, and multi-drone scenes.
* Established a public-trained baseline for future region-specific fine-tuning.
* Framed the project around public-to-local domain shift in drone detection.

---

## Methodology Overview

The project follows a baseline-first research workflow.

```text
Public Drone Dataset
        ↓
YOLO11 Training
        ↓
Public Dataset Evaluation
        ↓
Local Video Inference
        ↓
Qualitative Error Analysis
        ↓
Future Local Annotation Evaluation and Fine-Tuning
```

The important design choice is that **local videos were not used during training** in the current stage. This makes the local video evaluation useful for studying transfer behavior rather than memorization.

---

## System Pipeline

1. Prepare the public drone detection dataset in YOLO format.
2. Train a YOLO11 object detector on the public dataset.
3. Evaluate the trained model on the public validation/test split.
4. Collect local drone videos using a ground-based phone-camera perspective.
5. Run inference on the local videos using the public-trained YOLO11 model.
6. Inspect local predictions qualitatively.
7. Record false positives, missed detections, unstable detections, confidence behavior, and multi-drone issues.
8. Plan future formal evaluation using local annotations.
9. Fine-tune the public-trained model with local region-specific data in future stages.

---

## Dataset Overview

### Public Drone Dataset

The public dataset is used for training and public benchmark evaluation.

| Property     | Description                                 |
| ------------ | ------------------------------------------- |
| Dataset type | Ground-based public drone detection dataset |
| Format       | YOLO format                                 |
| Task         | Object detection                            |
| Class count  | 1                                           |
| Class name   | `drone`                                     |
| Use          | Training and public evaluation              |

The public dataset was prepared with separate image and label folders. A dataset configuration file defines the training, validation, and test paths along with the class name.

---

### Local Drone Videos

The local dataset contains 11 short drone video clips collected from a ground-based phone-camera viewpoint.

| Property            | Description                                                      |
| ------------------- | ---------------------------------------------------------------- |
| Number of clips     | 11 short local drone videos                                      |
| Camera viewpoint    | Ground-based phone-camera perspective                            |
| Lighting conditions | Day, evening, night, and night with heavy background lighting    |
| Drone scenes        | Single-drone and multi-drone clips                               |
| Background objects  | Buildings, windows, trees, branches, poles, humans, and open sky |
| Current use         | Qualitative local inference and error analysis                   |
| Future use          | Formal local mAP evaluation and region-specific fine-tuning      |

The local videos were used only for inference and qualitative transfer analysis in the current stage.

---

## Annotation Status

Local videos were annotated using bounding boxes around visible drones. The annotations were exported in COCO-style format.

However, the full local annotation evaluation pipeline has not been completed yet.

### Planned Annotation Pipeline

1. Extract frames from the original local videos.
2. Match extracted frames with local annotation files.
3. Convert COCO bounding boxes from:

```text
[x, y, width, height]
```

to YOLO normalized format:

```text
[class_id, x_center, y_center, width, height]
```

4. Create a local YOLO test dataset.
5. Run YOLO validation using the public-trained model.
6. Report local:

   * Precision
   * Recall
   * mAP@0.5
   * mAP@0.5:0.95

This future stage will convert the current qualitative local analysis into formal quantitative local evaluation.

---

## Model

The project uses **YOLO11**, a modern YOLO-family object detection model.

YOLO models are suitable for drone detection because they perform object localization and classification in a single detection pipeline. This makes them practical for video-based monitoring and future real-time detection systems.

### Training Configuration

| Parameter                         | Value / Description                                         |
| --------------------------------- | ----------------------------------------------------------- |
| Model family                      | YOLO11 object detection model                               |
| Training data                     | Public drone detection dataset only                         |
| Class count                       | 1                                                           |
| Class name                        | `drone`                                                     |
| Local data used in training       | No                                                          |
| Public evaluation                 | Precision, recall, mAP@0.5, mAP@0.5:0.95                    |
| Local evaluation in current stage | Video-level qualitative inference and confidence inspection |

---

## Evaluation Metrics

The public dataset evaluation uses standard object detection metrics.

### Precision

Precision measures how many predicted drone detections are actually correct.

```text
Precision = True Positives / (True Positives + False Positives)
```

High precision means the model produces fewer false alarms.

### Recall

Recall measures how many actual drones the model detects.

```text
Recall = True Positives / (True Positives + False Negatives)
```

High recall means the model misses fewer drones.

### mAP@0.5

mAP@0.5 measures mean average precision using an IoU threshold of 0.5.

### mAP@0.5:0.95

mAP@0.5:0.95 is stricter because it averages mAP across multiple IoU thresholds from 0.5 to 0.95.

### Confidence

For local video inference, average confidence was used as a diagnostic signal. However, confidence is not the same as accuracy. A high-confidence prediction can still be wrong if the model detects a human, branch, window, or pole as a drone.

---

## Public Dataset Results

The trained YOLO11 baseline achieved strong performance on the public drone dataset.

| Metric       |  Value |
| ------------ | -----: |
| Precision    | 0.9663 |
| Recall       | 0.9498 |
| mAP@0.5      | 0.9840 |
| mAP@0.5:0.95 | 0.7210 |

These results show that the YOLO11 model learned the public benchmark detection task effectively. The model was not weak on the dataset it was trained and evaluated on.

This is important because the local performance issues cannot simply be explained as a failed model training run. The issue is more likely caused by domain shift between the public dataset and the local video environment.

---

## Local Video Inference Summary

The public-trained YOLO11 model was tested on 11 local drone clips. The local results were evaluated qualitatively because formal local annotation-based validation was not completed in the current stage.

| Video | Condition                            | Detection Quality                                      | Main Issues                                                             | Avg Confidence |
| ----: | ------------------------------------ | ------------------------------------------------------ | ----------------------------------------------------------------------- | -------------: |
|     1 | Night                                | Main drone detected                                    | Many false positives                                                    |            62% |
|     2 | Day                                  | Poor detection quality                                 | False positives on humans                                               |            42% |
|     3 | Day                                  | Some drones detected for limited time                  | Many false positives; drones missed when background is present          |            59% |
|     4 | Day                                  | Drone detected when there is no background             | Many false positives                                                    |            43% |
|     5 | Evening                              | Some drones detected                                   | False positives; small branches identified as drones                    |            53% |
|     6 | Day                                  | Drone detected only up close and with clear background | Many false positives                                                    |            43% |
|     7 | Evening                              | One drone detected                                     | False positives on building top, pole, and trees; another drone ignored |            51% |
|     8 | Evening                              | Drone detected in clear sky                            | Constant false positives; detection ignored in some parts               |            59% |
|     9 | Night with heavy background lighting | Best result among local clips                          | False positives still occurred                                          |            67% |
|    10 | Night                                | One main drone detected                                | Another drone ignored; false positives on humans                        |            49% |
|    11 | Night                                | Main drone detected                                    | Detection missed at far range; many false positives                     |            62% |

The average confidence values ranged from **42% to 67%**. The highest confidence was observed in the night clip with heavy background lighting, but false positives still occurred. This confirms that confidence alone is not enough to judge real local accuracy.

---

## Public-to-Local Performance Gap

The public model performed strongly on the public dataset, but local video inference showed a noticeable public-to-local performance gap.

The model could detect drones in several local scenes, especially when the drone appeared against a clear sky or under favorable visibility. However, it also repeatedly confused local background objects with drones.

### Common False Positives

* Building windows
* Building tops
* Tree branches
* Poles
* Humans
* Rooftop structures
* Bright lights or night background objects

### Common Missed Detection Cases

* Far-away drones
* Drones blended with buildings or trees
* Drones appearing together in multi-drone scenes
* Drones visible only briefly
* Drones near complex backgrounds

The model learned drone-like appearance from the public dataset, but it did not learn enough local negative context. This is the central finding of the current project stage.

---

## Error Analysis

The observed local errors can be grouped into four main categories.

### 1. False Positives

The model sometimes predicted drones on non-drone objects such as humans, windows, tree branches, poles, and building tops.

This is a major practical issue because repeated false alarms can make a detection system unreliable.

### 2. Missed Detections

The model missed drones when they were far away, small, partially hidden, or visually blended into the background.

This is dangerous for real monitoring because the system may fail when the drone is hardest for humans to notice.

### 3. Unstable Detections

In some clips, the model detected a drone for only a short time and then lost it as the background changed.

This suggests that frame-level detection alone may not be enough. Future work may require temporal smoothing, tracking, or video-based detection logic.

### 4. Multi-Drone Failure

In some local videos, one drone was detected while another drone in the same scene was ignored.

This suggests that the model may struggle when multiple drones appear at different scales, distances, or background positions.

---

## Why Hard Negative Samples Matter

The local results show that future training should not only collect more drone-positive examples. It should also include hard negative examples.

Hard negatives are objects that look similar to drones or frequently cause false positives.

Important hard negative classes/scenes include:

* Empty sky
* Birds
* Kites
* Airplanes
* Small windows
* Tree branches
* Rooftop edges
* Building corners
* Poles
* Humans
* Light sources
* Night glare
* Distant moving objects

Adding these samples can teach the model what is not a drone in the target local region.

---

## Repository Structure

```text
region-specific-drone-detection-yolo11/
│
├── README.md
├── LICENSE
│
├── notebooks/
│   ├── yolo_training.ipynb
│   └── yolo_eval.ipynb
│
├── results/
│   ├── tables/
│   │   ├── public_dataset_metrics.csv
│   │   └── local_video_qualitative_summary.csv
│   └── figures/
│       ├── public_dataset_metrics.png
│       └── local_video_confidence.png
│
├── docs/
│   ├── methodology.md
│   ├── local_error_analysis.md
│   └── future_work.md
```

---

## Requirements

Main dependencies:

```text
ultralytics
opencv-python
numpy
pandas
matplotlib
pyyaml
tqdm
scikit-learn
jupyter
```

If using GPU acceleration, install the correct PyTorch version for your CUDA environment before installing Ultralytics.

---

## Dataset Format

The public dataset should follow YOLO detection format.

```text
dataset/
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

Example `data.yaml`:

```yaml
path: /path/to/dataset
train: images/train
val: images/val
test: images/test

names:
  0: drone
```

---

## Example Python Inference

```python
from ultralytics import YOLO

model = YOLO("runs/detect/train/weights/best.pt")

results = model.predict(
    source="data/local_videos/sample.mp4",
    conf=0.25,
    save=True
)
```

---

## Visual Results

Place result figures inside `results/figures/`.

### Public Dataset Metrics

![Public Dataset Metrics](results/figures/public_dataset_metrics.png)

### Local Video Confidence

![Local Video Confidence](results/figures/local_video_confidence.png)

---

## Results Files

Recommended result files:

### `results/tables/public_dataset_metrics.csv`

```csv
metric,value
precision,0.9663
recall,0.9498
mAP@0.5,0.9840
mAP@0.5:0.95,0.7210
```

### `results/tables/local_video_qualitative_summary.csv`

```csv
video,condition,detection_quality,issues,avg_confidence
1,Night,Main drone detected,Many false positives,0.62
2,Day,Poor detection quality,False positives on humans,0.42
3,Day,Some drones detected for limited time,Many false positives and missed detections with background,0.59
4,Day,Drone detected when there is no background,Many false positives,0.43
5,Evening,Some drones detected,False positives on small branches,0.53
6,Day,Drone detected only up close and with clear background,Many false positives,0.43
7,Evening,One drone detected,False positives on building top pole and trees; another drone ignored,0.51
8,Evening,Drone detected in clear sky,Constant false positives and ignored detections in some parts,0.59
9,Night with heavy background lighting,Best result among local clips,False positives still occurred,0.67
10,Night,One main drone detected,Another drone ignored and false positives on humans,0.49
11,Night,Main drone detected,Missed at far range and many false positives,0.62
```

---

## Main Findings

* The YOLO11 model achieved strong public dataset performance.
* Public benchmark success did not fully transfer to local drone videos.
* The model detected drones better when the background was clear.
* False positives appeared frequently on local background objects.
* Humans, windows, branches, poles, and building tops were common false-positive sources.
* Multi-drone scenes created additional detection failures.
* Average confidence was useful for diagnosis but not enough to prove accuracy.
* Local annotations are necessary for formal precision, recall, and mAP evaluation.
* Region-specific fine-tuning is likely necessary for practical local deployment.

---

## Limitations

The current project has several important limitations.

### 1. No Formal Local mAP Yet

The public dataset result is quantitative, but the local video result is qualitative. Local precision, recall, and mAP were not calculated in the current stage.

### 2. Small Local Dataset

The local dataset contains only 11 short clips. More footage is needed across different locations, lighting conditions, weather conditions, backgrounds, drone distances, and camera angles.

### 3. No Local Fine-Tuning Yet

The current model was trained only on the public dataset. No local frames were used during training.

### 4. Confidence Is Not Accuracy

Average confidence values were useful for observing model behavior, but confidence alone does not prove correct detection.

### 5. No Real-Time Deployment Test

The model was tested through offline video inference. Real-time camera deployment, FPS, edge-device constraints, and alert logic were not evaluated.

### 6. Privacy Considerations

Some local videos contain humans. Any future dataset release or visual examples should avoid exposing identifiable people unless consent is obtained.

---

## Future Work

The next stage of the project should focus on turning the qualitative local analysis into formal local evaluation and then testing region-specific improvement.

### Planned Improvements

* Convert local COCO annotations into YOLO format.
* Extract local video frames and match them with ground-truth annotations.
* Run formal local YOLO validation.
* Report local precision, recall, mAP@0.5, and mAP@0.5:0.95.
* Collect more local footage.
* Add hard negative samples.
* Fine-tune the public-trained model using local regional data.
* Compare public-only, local-only, and public-plus-local training.
* Evaluate separate subsets:

  * Day
  * Evening
  * Night
  * Heavy lighting
  * Clear sky
  * Background clutter
  * Multi-drone scenes
* Add object tracking for temporal stability.
* Test real-time deployment using webcam or edge device.
* Compare YOLO11 with other detectors such as Faster R-CNN, DETR, RT-DETR, or newer YOLO variants.

---

## Expected Final Research Direction

The final version of this project should answer:

1. How well does a public-trained drone detector perform on local regional videos?
2. What are the main local false-positive and false-negative sources?
3. Does local fine-tuning improve detection reliability?
4. Is public + local training better than public-only training?
5. Which local conditions benefit most from region-specific adaptation?
6. Can the model run in real time for practical camera-based monitoring?

---

## Technical Skills Demonstrated

This project demonstrates practical experience with:

* Computer vision
* Object detection
* YOLO11 / Ultralytics
* Dataset preparation
* YOLO annotation format
* COCO-to-YOLO annotation conversion
* Video inference
* Model evaluation
* Precision, recall, and mAP analysis
* Domain shift analysis
* Error analysis
* Research-oriented ML experimentation
* Python scripting
* OpenCV
* Google Colab / Kaggle experimentation
* Responsible interpretation of incomplete results

---

## Responsible Use and Privacy

Drone detection systems can support safety, restricted-area monitoring, and unauthorized drone awareness. However, camera-based detection systems can also capture people, homes, buildings, and public spaces.

Future dataset handling should follow responsible practices:

* Blur identifiable faces if frames are shared publicly.
* Avoid publishing private locations without permission.
* Do not expose unnecessary personal information.
* Clearly state dataset limitations.
* Avoid claiming deployment readiness before real-world validation.
* Evaluate both false positives and false negatives carefully.

---

## Project Planning Summary

| Phase   | Work                                                                                |
| ------- | ----------------------------------------------------------------------------------- |
| Phase 1 | Problem selection, literature review, and research framing                          |
| Phase 2 | Public dataset preparation and YOLO11 baseline training                             |
| Phase 3 | Public dataset evaluation using precision, recall, and mAP                          |
| Phase 4 | Local video collection under day, evening, night, glare, and multi-drone conditions |
| Phase 5 | Local video inference and qualitative error analysis                                |
| Phase 6 | Documentation and repository organization                                           |
| Phase 7 | Future local annotation conversion and formal local evaluation                      |
| Phase 8 | Future region-specific fine-tuning and comparison                                   |

---

## Authors

* Dewan Mohammad Saif
* Karima Akter Kanu

Faculty Advisor:

* Shahnewaz Siddique
  Associate Professor
  Department of Electrical and Computer Engineering
  North South University

---

## References

[1] J. Redmon, S. Divvala, R. Girshick, and A. Farhadi, “You Only Look Once: Unified, Real-Time Object Detection,” Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition, 2016.

[2] A. Bochkovskiy, C.-Y. Wang, and H.-Y. M. Liao, “YOLOv4: Optimal Speed and Accuracy of Object Detection,” arXiv preprint arXiv:2004.10934, 2020.

[3] T.-Y. Lin et al., “Microsoft COCO: Common Objects in Context,” European Conference on Computer Vision, 2014.

[4] S. J. Pan and Q. Yang, “A Survey on Transfer Learning,” IEEE Transactions on Knowledge and Data Engineering, vol. 22, no. 10, pp. 1345–1359, 2010.

[5] C. Shorten and T. M. Khoshgoftaar, “A Survey on Image Data Augmentation for Deep Learning,” Journal of Big Data, vol. 6, no. 60, 2019.

[6] Ultralytics, “YOLO Documentation and Model Training Workflow.”

---

## Summary

This repository presents a YOLO11-based public dataset baseline for ground-based drone detection and evaluates its transfer behavior on local drone videos. The model achieved strong public dataset performance, but local video inference revealed false positives, missed detections, and instability under real local conditions.

The project shows why region-specific evaluation matters. A detector that performs well on a public dataset may still require local data, hard negative samples, and fine-tuning before it can be trusted in a real deployment environment.

