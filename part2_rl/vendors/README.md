# Diffusion Policy Environment Setup and Evaluation Guide

## 1. 安裝必要套件
```bash
sudo apt install -y libosmesa6-dev libgl1-mesa-glx libglfw3 patchelf
```

## 2. 建立虛擬環境
```bash
conda env create -f conda_environment.yaml
```

## 3. 安裝 WandB 與 HuggingFace Hub
```bash
pip install wandb
pip install huggingface_hub==0.25.2
```

## 4. 建立資料資料夾
```bash
mkdir data && cd data
```

## 5. 下載 PushT 資料集
```bash
# https://drive.google.com/drive/folders/1tL7WRNSsIjPAGuD5yAJuRMJKIe-DAz0J
```

## 6. 下載訓練好的模型（放入 `data` 目錄）
```bash
wget -O data/epoch=0550-test_mean_score=0.969.ckpt https://diffusion-policy.cs.columbia.edu/data/experiments/low_dim/pusht/diffusion_policy_cnn/train_0/checkpoints/epoch=0550-test_mean_score=0.969.ckpt
```

## 7. 執行模型評估

### 7.1 設定環境變數
```bash
export HYDRA_FULL_ERROR=1
```

### 7.2 執行評估指令
```bash
python eval.py --checkpoint data/epoch=0550-test_mean_score=0.969.ckpt --output_dir data/pusht_eval_output
```


### 7.3 訓練指令

```bash
export MUJOCO_GL=egl
export SDL_VIDEODRIVER=dummy
export PYOPENGL_PLATFORM=egl
export EGL_PLATFORM=drm
unset DISPLAY
export WANDB_DISABLED=true

python train.py \
--config-dir=. \
--config-name=image_pusht_diffusion_policy_cnn.yaml \
dataloader.batch_size=16 val_dataloader.batch_size=16 \
dataloader.num_workers=0 val_dataloader.num_workers=0 \
training.num_epochs=200 training.rollout_every=1 \
training.sample_every=999999 training.checkpoint_every=10 \
task.env_runner.n_train=1 task.env_runner.n_test=1 \
task.env_runner.n_train_vis=0 task.env_runner.n_test_vis=0 \
logging.mode=offline training.device=cuda:0
```