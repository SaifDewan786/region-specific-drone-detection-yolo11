# Future Work

This project is currently in the public-baseline and local-transfer evaluation stage. The trained YOLO11 model achieved strong performance on the public drone dataset, but local video inference showed false positives, missed detections, and unstable detection behavior under region-specific conditions.

The next stage of the project should convert the current qualitative findings into a formal local-domain evaluation and then test whether region-specific fine-tuning improves practical drone detection performance.

---

## 1. Convert Local Annotations into YOLO Format

The local videos have already been annotated using bounding boxes, but the local annotation-based evaluation pipeline has not been completed yet.

The next step is to convert the existing COCO-style annotations into YOLO format.

### Planned conversion workflow

1. Extract frames from the original local drone videos.
2. Match each extracted frame with its corresponding annotation.
3. Convert COCO bounding boxes from:

```text
[x, y, width, height]
```

to YOLO normalized format:

```text
[class_id, x_center, y_center, width, height]
```

4. Create the YOLO dataset directory structure:

```text
local_yolo_dataset/
│
├── images/
│   ├── val/
│   └── test/
│
├── labels/
│   ├── val/
│   └── test/
│
└── data.yaml
```

5. Verify the converted annotations visually before evaluation.

This step is important because the current local results are qualitative. Formal annotation conversion will allow proper local precision, recall, and mAP calculation.

---

## 2. Formal Local Dataset Evaluation

After converting the local annotations into YOLO format, the public-trained YOLO11 model should be evaluated on the local dataset.

The local evaluation should report:

* Precision
* Recall
* mAP@0.5
* mAP@0.5:0.95
* False-positive count
* False-negative count
* Detection confidence distribution

This will establish a proper **local-domain baseline**.

The key comparison will be:

```text
Public-trained YOLO11 model → Local annotated test set
```

This result will show how much public dataset performance drops when the same model is tested under local regional conditions.

---

## 3. Expand the Local Dataset

The current local dataset contains 11 short drone clips. This is useful for preliminary analysis, but it is not enough for a strong region-specific drone detection study.

Future data collection should include more variation across:

* Daytime scenes
* Evening scenes
* Night scenes
* Heavy artificial lighting
* Glare
* Cloudy sky
* Clear sky
* Urban backgrounds
* Open-field backgrounds
* Rooftop scenes
* Tree-heavy scenes
* Multi-drone scenes
* Far-range drones
* Close-range drones
* Fast-moving drones
* Drones partially hidden by background objects

A larger and more diverse local dataset will make the region-specific evaluation more reliable.

---

## 4. Add Hard Negative Samples

The local qualitative analysis showed that the public-trained model often confused non-drone objects with drones.

Future training should include hard negative examples such as:

* Empty sky
* Birds
* Kites
* Airplanes
* Tree branches
* Building windows
* Building edges
* Rooftop structures
* Poles
* Humans
* Distant lights
* Night glare
* Small moving background objects

Hard negatives are important because they teach the model what is **not** a drone in the local region.

This should reduce false positives during real-world local deployment.

---

## 5. Region-Specific Fine-Tuning

After creating a verified local dataset, the public-trained YOLO11 model should be fine-tuned using local drone footage.

The main experiment should compare:

| Training Setup                  | Purpose                                             |
| ------------------------------- | --------------------------------------------------- |
| Public-only training            | Baseline model                                      |
| Local-only training             | Measures performance from local data alone          |
| Public + local fine-tuning      | Tests whether local adaptation improves performance |
| Public + local + hard negatives | Tests whether false positives are reduced           |

The expected research question is:

> Does fine-tuning with local regional data improve drone detection performance under local conditions compared with a model trained only on public drone data?

This is the core future direction of the project.

---

## 6. Evaluate by Scene Type

Future evaluation should not only report one overall mAP score. The model should also be evaluated separately across different local scene types.

Recommended scene categories:

* Clear sky
* Background clutter
* Daytime
* Evening
* Night
* Heavy lighting
* Multi-drone
* Far-range drone
* Close-range drone
* Human-present scene
* Building-heavy scene
* Tree-heavy scene

This will show where local fine-tuning helps most and where the model still fails.

---

## 7. Add Object Tracking for Video Stability

The current model performs frame-level detection. In local videos, some detections were unstable: the model detected the drone for a short time and then lost it.

Future work should add tracking methods such as:

* ByteTrack
* DeepSORT
* Optical-flow-based tracking
* Simple IoU-based frame-to-frame tracking

Tracking can help reduce flickering detections and improve video-level reliability.

---

## 8. Real-Time Deployment Testing

The current project uses offline video inference. Future work should test whether the model can run in real time.

Important deployment metrics include:

* FPS
* Inference latency
* GPU memory usage
* CPU usage
* Edge-device compatibility
* Webcam or IP camera support
* Alert generation reliability

Possible deployment targets:

* Laptop webcam
* Mobile camera
* Raspberry Pi with accelerator
* Jetson Nano / Jetson Orin
* Low-cost surveillance camera system

---

## 9. Compare YOLO11 with Other Detection Models

YOLO11 should be compared with other object detection models to determine whether it is the best choice for this task.

Possible comparison models:

* YOLOv8
* YOLOv10
* YOLO11
* Faster R-CNN
* DETR
* RT-DETR
* EfficientDet

The comparison should include both accuracy and inference speed.

Recommended metrics:

* Precision
* Recall
* mAP@0.5
* mAP@0.5:0.95
* FPS
* Inference latency
* Model size

---

## 10. Improve Responsible Dataset Handling

Some local videos contain humans and real local environments. Future dataset handling should consider privacy and responsible use.

Recommended practices:

* Blur identifiable faces before publishing sample frames.
* Avoid exposing private locations.
* Remove unnecessary personal details.
* Keep raw video data private if consent is not available.
* Publish only anonymized sample outputs.
* Clearly document dataset limitations.

---

## Final Future Goal

The final goal is to build a complete region-specific drone detection study that answers:

1. How well does a public-trained YOLO11 detector perform on local drone videos?
2. What local objects cause the most false positives?
3. Does local fine-tuning improve detection reliability?
4. Does adding hard negative local samples reduce false alarms?
5. Can the final model run reliably in real-time local deployment?

The current project establishes the baseline and identifies the public-to-local performance gap. The next stage should complete local annotated evaluation and region-specific fine-tuning.

