# Part 1 — YOLOv11-Seg ROS Pipeline

This section describes how to process ROS bag data, generate training data with Grounded-SAM2, and train a YOLOv11 segmentation model.

---

## 1. Clone the Repository

```bash
git clone --recursive git@github.com:Leo5050xvjf/cv-rl-assessment.git
cd cv-rl-assessment/part1_cv/yolov11-seg_ros
```

---

## 2. Prepare ROS Data


Place your `.db3` file under the following path:

```
ros_data/
└── interview_perception_exam01/
    ├── interview_perception_exam01_0.db3
    ├── metadata.yaml
```

---

## 3. Convert ROS Bag to MP4

Run the conversion script to extract RGB frames and convert them into an MP4 video:

```bash
bash python scripts/bag_to_frames.py && mv datasets/frames/video/rgb.mp4 ../vendors/Grounded-SAM-2/asset/kitchen.mp4
```

---

## 4. Generate Training Dataset (8/1/1 Split)

Switch to the Grounded-SAM2 directory and generate pseudo-labels for training:

```bash
cd ../vendors/Grounded-SAM-2
bash scripts/generate_training_data.sh
```

---

## 5. Train YOLOv11-Seg

Finally, go back to the YOLOv11-Seg directory and start training:

```bash
cd ../yolov11-seg_ros/ultralytics
bash train.sh
```

---



## Results

| Label Visualization | Detection Results |
|----------------------|-------------------|
| ![Labels](part1_cv/res/labels.jpg) | ![Results](part1_cv/res/results.png) |




## Notes

- Ensure that the Grounded-SAM2 environment (`gsam2`) is properly set up.
- Generated pseudo-labels should contain all 4 target classes before training.
- The `train.sh` script assumes dataset paths are configured in `dataset.yaml`.
