git clone --recursive git@github.com:Leo5050xvjf/cv-rl-assessment.git
cd cv-rl-assessment/part1_cv/yolov11-seg_ros

# 1) 放 db3
# ros_data/interview_perception_exam01/interview_perception_exam01_0.db3

# 2) 轉 mp4 並移至GDSAM2 專案
bash python scripts/bag_to_frames.py && mv datasets/frames/video/rgb.mp4 ../vendors/Grounded-SAM-2/asset/kitchen.mp4

# 3) 產訓練集（8/1/1）
cd cv-rl-assessment/part1_cv/venders/Grounded-SAM-2
bash scripts/generate_training_data.sh

# 4) 訓練
cd cv-rl-assessment/part1_cv/yolov11-seg_ros/ultralytics
bash train.sh
