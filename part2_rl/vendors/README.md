## 環境設定（Setup）
-  **OS:** Ubuntu 24.04  
-  **開發環境:** WSL2 + VSCode  
-  **工具:** GDSAM2 + Ultralytics YOLOv11n-seg  

## 結果(Results)
- 在 80epoch 時, 模型已經大致可以將方塊推至目標區域附近
- **v1**: 約在 **60 epochs** 時成功率達 **0.8** , 因硬體限制，尚未完成訓練
- **v2**: 因硬體限制，尚未完成訓練

## 實作過程（implementation process）
1. Clone **Diffusion Policy (DP)** 專案並下載公司數據  
2. 根據個人硬體條件設置訓練參數(part2.ipynb)
3. 模型選擇: diffusion_policy.policy.diffusion_unet_hybrid_image_policy.DiffusionUnetHybridImagePolicy

## 分析與觀察（analysis and observations）
-  在 **50 epochs 以前**，模型幾乎無法穩定找到方塊位置，或一旦出現偏差就無法修正。  
-  在 **50 epochs 之後**，可明顯感受到模型更容易維持方塊於目標區域內。  
-  即使在後期，當發生較大偏差時，模型仍會陷入無法回復正確軌跡的情況。