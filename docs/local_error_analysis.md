# Local Error Analysis

This document summarizes the local error patterns observed when the public-dataset-trained YOLO11 drone detector was tested on locally collected drone videos.

The current local evaluation is qualitative. Formal local precision, recall, and mAP were not calculated yet because the local annotation-based YOLO validation pipeline is still future work.

---

## 1. Local Evaluation Context

The YOLO11 model was trained only on a public ground-based drone detection dataset. No local video frames were used during training in the current stage.

The trained model was then tested on 11 local drone clips collected from a ground-based phone-camera perspective.

The local videos included:

* Day scenes
* Evening scenes
* Night scenes
* Night scenes with heavy background lighting
* Single-drone scenes
* Multi-drone scenes
* Humans in frame
* Buildings
* Windows
* Tree branches
* Poles
* Open sky
* Background clutter

The purpose was to observe whether public dataset performance transfers reliably to local conditions.

---

## 2. Local Video Summary

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

The confidence values ranged from 42% to 67%. However, confidence should not be treated as accuracy because a confident prediction can still be incorrect.

---

## 3. Main Error Categories

The local errors can be grouped into four main categories.

---

## 3.1 False Positives

False positives occurred when the model predicted a drone on an object that was not actually a drone.

### Common false-positive objects

* Humans
* Building windows
* Building tops
* Rooftop structures
* Tree branches
* Poles
* Distant lights
* Background clutter

### Why this happened

The model was trained on a public dataset, so it learned general drone appearance. However, it did not learn enough local negative context. Objects such as small windows, tree branches, poles, and people may visually resemble drone-like shapes at low resolution.

### Practical impact

False positives are a major issue for deployment. If the system frequently detects non-drone objects as drones, users may stop trusting the detector.

---

## 3.2 Missed Detections

Missed detections occurred when the drone was visible in the video but the model failed to detect it.

### Common missed-detection conditions

* Drone far from the camera
* Drone appearing very small
* Drone blending with trees or buildings
* Drone moving near background clutter
* Drone appearing together with another drone
* Drone visible only briefly
* Poor lighting or glare

### Why this happened

Small drones can occupy very few pixels in a frame. When they appear near buildings, trees, poles, or lights, their shape becomes harder to separate from the background.

### Practical impact

Missed detections are dangerous because the system may fail exactly when the drone is difficult for humans to notice.

---

## 3.3 Unstable Detections

Some detections were unstable across video frames. The model detected a drone for a short time but then lost it as the drone moved or as the background changed.

### Possible causes

* Frame-level detection without temporal tracking
* Drone scale changing across frames
* Background clutter
* Motion blur
* Low confidence near the detection threshold
* Drone moving into visually complex regions

### Practical impact

Unstable detection creates flickering outputs. In a real monitoring system, this could make alerts inconsistent.

### Future fix

Add tracking or temporal smoothing, such as:

* ByteTrack
* DeepSORT
* IoU-based tracking
* Confidence smoothing
* Consecutive-frame confirmation before alerting

---

## 3.4 Multi-Drone Failure

In some clips, one drone was detected while another drone in the same scene was ignored.

### Possible causes

* One drone was smaller or farther away
* Different drones appeared against different backgrounds
* The model focused on the more visible drone
* The second drone had lower contrast
* Background clutter reduced detection confidence

### Practical impact

A drone detection system should detect all visible drones, not just the easiest one. Multi-drone failure is important for surveillance and safety use cases.

---

## 4. Public-to-Local Domain Shift

The public dataset result showed strong quantitative performance, but the local video result showed practical instability.

This suggests a public-to-local domain shift.

### Public dataset environment

The public dataset likely contains drone examples that are useful for general training, but it may not fully represent the local deployment environment.

### Local environment

The local clips included:

* Local buildings
* Local tree lines
* Phone-camera footage
* Mixed lighting
* Human presence
* Rooftop structures
* Far-range drones
* Night lighting
* Background clutter

These local visual patterns caused both false positives and missed detections.

---

## 5. Confidence Score Interpretation

Average confidence was recorded for the local video clips, but confidence should be interpreted carefully.

A high confidence score means the model is confident in its prediction. It does not guarantee the prediction is correct.

For example:

* A model may confidently detect a tree branch as a drone.
* A model may confidently detect a human as a drone.
* A model may miss a real drone but still produce high-confidence false positives elsewhere.

Therefore, confidence is useful for diagnosing model behavior, but it cannot replace formal ground-truth evaluation.

---

## 6. Key Local Findings

The local evaluation showed that:

* The public-trained model can detect drones in several local scenes.
* Detection is better when the drone appears against a clear sky.
* Detection becomes weaker when the drone appears near buildings, trees, or humans.
* False positives are frequent in cluttered local backgrounds.
* Humans, branches, poles, and windows are important hard negative sources.
* Multi-drone scenes are more difficult than single-drone scenes.
* Average confidence alone is not enough to measure local accuracy.
* Formal local annotation-based evaluation is necessary.

---

## 7. Recommended Fixes

Based on the observed local errors, the following improvements are recommended.

### Add local hard negatives

Include local examples of:

* Empty sky
* Birds
* Kites
* Humans
* Tree branches
* Poles
* Windows
* Building edges
* Rooftops
* Night lights

### Perform local fine-tuning

Fine-tune the public-trained YOLO11 model using local drone footage and hard negative samples.

### Run formal local evaluation

Convert local annotations into YOLO format and calculate:

* Precision
* Recall
* mAP@0.5
* mAP@0.5:0.95

### Add video tracking

Use tracking to improve temporal stability and reduce frame-level flickering.

### Evaluate by condition

Measure performance separately for:

* Day
* Evening
* Night
* Clear sky
* Background clutter
* Multi-drone scenes
* Far-range drones

---

## 8. Conclusion

The local error analysis shows that the public-trained YOLO11 model learned general drone appearance but did not fully adapt to local visual conditions.

The main problem is not complete detection failure. The model can detect drones in several local scenes. The bigger issue is local reliability: false positives, missed detections, unstable frame-level outputs, and multi-drone failure.

This supports the need for region-specific data, hard negative samples, formal local evaluation, and local fine-tuning in future work.

