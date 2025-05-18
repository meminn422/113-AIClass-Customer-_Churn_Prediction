# 客戶流失預測報告書

## 一、專題背景說明

### 為什麼客戶流失預測很重要
客戶流失預測能幫助企業提前識別可能會流失的顧客，進而可以主動採取行動去挽留客戶。預測技術提供了一種預防式經營方式，使企業能夠在客戶仍有挽回空間時介入。

### 實際應用場景

#### 1. 電信產業
- **數據來源：** 通話紀錄、流量使用趨勢、資費變動紀錄、退租頁面瀏覽紀錄等。
- **應用：** 預測跳槽風險，推送優惠續約方案，優先處理高風險客戶來電。

#### 2. 訂閱服務
- **數據來源：** 登入頻率、觀看時長、喜好內容、取消訂閱時間點與原因。
- **應用：** 預測潛在流失者、優化推薦內容、對試用期用戶推送優惠。

#### 3. 金融科技
- **數據來源：** 存款、轉帳、交易頻率、App 開啟次數與功能使用分布。
- **應用：** 預測低使用率帳戶、推播金流優惠、推薦理財商品。

---

## 二、資料描述與處理方式

- **資料集：** `churn-bigml-80.csv` (2666 筆)、`churn-bigml-20.csv` (667 筆)
- **欄位數：** 20 欄
- **缺失值處理：** 無缺失值，無需補值。

### 類別變數轉換
| 原欄位 | 原始值 | 數值轉換後 |
| ------ | ------ | -------- |
| International plan | Yes / No | 1 / 0 |
| Voice mail plan | Yes / No | 1 / 0 |
| Churn | True / False | 1 / 0 |

### 特徵選擇與新增特徵
- **移除欄位：** State, Area code
- **新增欄位：**
  - `many_service_calls`: 客服次數超過 3 次
  - `no_voicemail_usage`: 語音留言數量為 0
  - `avg_call_duration_day`: 日通話平均時長
  - `high_usage`: 日通話分鐘數是否高於平均

---

## 三、模型建立與參數設定

| 模型 | 主要參數 | 說明 |
| ---- | -------- | ---- |
| Logistic Regression | max_iter=1000 | 防止迭代不足導致未收斂 |
| KNN | n_neighbors=5 | 常見預設值，可平衡擬合度 |
| Random Forest | n_estimators=100, random_state=42 | 增加樹數提升穩定性 |
| XGBoost | use_label_encoder=False, eval_metric='logloss' | 關閉 label encoder，指定對數損失 |

---

## 四、模型比較與分析

| 模型 | Accuracy | Precision | Recall | F1-score | ROC AUC |
| ---- | -------- | --------- | ------ | -------- | ------- |
| Logistic Regression | 0.87 | 0.74 | 0.60 | 0.66 | 0.85 |
| KNN | 0.84 | 0.60 | 0.51 | 0.55 | 0.60 |
| Random Forest | 0.94 | 0.92 | 0.75 | 0.83 | 0.87 |
| XGBoost | 0.95 | 0.94 | 0.79 | 0.86 | 0.90 |

**結論：** XGBoost 在各項指標中均表現最佳，特別是在 Recall 和 AUC 上顯著優於其他模型。

---

## 五、模型改進建議

- **資料不平衡處理：** 使用 SMOTE 或 `class_weight='balanced'`
- **模型解釋性提升：** 引入 SHAP 分析、生成客戶等級報表。
- **特徵擴充：** 增加「客服聯絡內容」、「資費變更紀錄」等行為數據。
- **模型部署考量：** 定期 retrain、建立 alert 系統、確保預測解釋性。

---

## 六、專案資源連結

- GitHub Repo: [113-AIClass-Customer_Churn_Prediction](https://github.com/meminn422/113-AIClass-Customer-_Churn_Prediction)
- Google Colab: [完整模型訓練程式碼](https://drive.google.com/file/d/1WUCK0DkmD7gosCeTKV6BxV3yLo0zukix/view?usp=sharing)
- GridSearchCV: [超參數調整程式碼](https://colab.research.google.com/drive/1s0YHZYMCGiUua165rojGPLVUNniCd6fR?usp=sharing)
