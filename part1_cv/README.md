## 環境設定（Setup）
1. **OS:** Ubuntu 24.04  
2. **開發環境:** WSL2 + VSCode  
3. **工具:** GDSAM2 + Ultralytics YOLOv11n-seg  

---

## 結果（Results）
1. **訓練集:** 約 **0.9 mAP(Box)**、約 **0.8 mAP(Mask)**  
2. **測試集:** 尚未驗證  

---

## 實作過程（Implementation Process）
1. 將 **RGBT** 數據中的 **RGB** 影像提取並輸出成 `kitchen.mp4`。  
2. 使用 **GDSAM2** 將 `kitchen.mp4` 轉換為訓練數據。  
3. 透過 **Ultralytics YOLOv11n-seg** 進行訓練，結果輸出至：  


---

## 分析與觀察（Analysis and Observations）

### 偽標註（Pseudo Labeling）
1. 由於 tracking 方式容易將錯誤標籤延續至後續預測，因此選擇 **每張影像獨立預測**。  
2. 預測類別共 4 種：**炒鍋、鍋鏟、鐵碗、雞蛋**。  
- 需調整 **Prompt、信心閾值、啟發式規則（物體大小）** 以獲得更佳偽標籤結果。  
3. **GDSAM2 對於鐵鍋的預測已相當精準**，因此目前專案未採用熱力圖資訊。  
4. **不同 Prompt 對預測結果影響極大**：  
- `"metal bowl"` 信心值約 0.2–0.3  
- `"mixing bowl"` 信心值約 0.45–0.6  

---

### 模型訓練（Model Training）
1. 選擇 **Ultralytics YOLOv11n-seg** 的原因如下：  
a. 專案安裝簡易、**環境相容性高**，無額外依賴衝突。  
b. **YOLOv11n-seg** 為 YOLO 系列中輕量但具 SOTA 表現的版本，性能穩定。  
c. 由於數據重複性高，為避免 **overfitting**，選擇此輕量模型作為起始點。  
2. **Ultralytics 預設包含多種資料增強（data augmentation）策略**，有助於提升模型泛化能力。
