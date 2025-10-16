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



## 簡答題
1. 在你看來，機器人在執行料理任務的過程中，有哪些關鍵性的 Perception 資訊是機器人 必須精確掌握的？你會如何設計系統或方法來實作並有效獲取這些 Perception 資訊？ 

```text
料理任務中最關鍵的感知資訊包含：**物體定位與分割（wok、ladle、egg、bowl）**、**溫度分布（thermal**） 以及 **物體狀態變化（生蛋→熟蛋）**。
我會結合 **Grounding DINO + SAM2** 進行多物體自動標註，並以 **YOLOv11n-seg** 訓練即時分割模型。
另外，利用 RGB + Thermal 融合輸入 提升對高溫區域的識別準確度，確保機器人能精準、安全地操作。
```
2. 假設上述的感知模型已經訓練完成，且達到理想的準確度與可靠性，你接下來會如何將這個模型實際應用在機器人的料理任務流程當中？

```text
當感知模型訓練完成後，我會將其整合入料理機器人的 Perception → Planning → Control 流程。
Perception:
模型會從相機獲取的 RGB 與熱像（Thermal）影像中，辨識出鍋子、鍋鏟、雞蛋、容器等，並輸出它們的BBox與Mask。
Thermal channel 則用於監測鍋面溫度分布，例如判別「鍋內油溫是否達到下蛋條件」或「食材是否過熟」。

Planning:

感知結果會提供給高層規劃模組，根據當前物體位置與狀態，決定下一步操作策略。
例如：
a. 雞蛋變色了要起鍋。
b. 油溫到了要下菜

動作執行階段（Control）
規劃模組生成的動作指令會傳遞至運動控制器生成機械臂的目標軌跡。
在執行期間，感知模型會持續監測物體的位姿變化，提供回饋給控制模組，形成 closed-loop feedback control，讓機器人能在食材位置偏移或器具滑動時自動修正動作。

```