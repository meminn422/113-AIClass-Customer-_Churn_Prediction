客戶流失預測報告書

 一、專題背景說明
為什麼客戶流失預測很重要
因為它能幫助企業提前識別可能會流失的顧客，進而可以主動採取行動去挽留客戶，預測技術提供一種預防式經營，讓企業能在客戶仍有挽回空間時介入。
這個問題在產業端的實際應用（電信、訂閱服務、金融科技）
電信
可能會使用客戶的通話與上網紀錄、資費變動紀錄、近期是否瀏覽過退租相關頁面等資料進行分析。
預測：透過分析通話時間、流量使用趨勢、客服投訴紀錄等，模型可以提前預測哪些用戶可能即將跳槽至競爭對手方
針對性促銷：企業可以針對即將跳槽的客戶自動推播優惠續約方案或客製化資費
客戶優先處理排程：高風險流失客戶來電時，系統會優先轉給較高階的服務人員處理
訂閱服務
可能會使用客戶的登入頻率、觀看時長資料、喜好內容類型、過去取消訂閱的時間點與原因等資料進行分析
預測：分析用戶近30天的使用頻率，找出「活躍度大幅下降」的潛在流失者
推薦內容優化：強化個人化推薦演算法，來降低客戶流失的可能性
對試用期用戶建模，預測哪些人更有可能成為付費用戶，並針對潛在流失者推優惠
金融科技
		利用客戶的存款、轉帳、交易頻率以及App開啟次數與功能使用分布
低使用率帳戶預警：如用戶長期無交易或資金閒置，預測其可能流失或轉向競爭平台
推播金流優惠：推送轉帳回饋、交易折扣、定存高利等誘因給風險用戶
投資用戶個性化推薦：針對風險偏好預測，推薦合適理財商品，避免因為「沒興趣」或「覺得太難」而退出平台
目標：本報告將使用機器學習模型預測客戶是否會流失。

二、資料描述與處理方式
說明使用的資料集（churn-bigml-80.csv、churn-bigml-20.csv），資料筆數與欄位數。
churn-bigml-80.csv：2666筆資料、20欄
churn-bigml-20.csv：667筆資料、20欄
重要欄位摘要
![image](https://github.com/user-attachments/assets/b482fe6a-b172-41cd-8b96-f372c9f596a1)
是否有缺失值？如何填補或處理？
資料中無缺失值（missing values）。
所有欄位皆為完整資料，無需進行補值或刪除欄位處理。
將類別變數轉成數值（例如：Yes/No → 1/0）
是，International plan、Voice mail plan、Churn
刪除或保留欄位：例如移除 State 欄位的原因說明
在預處理過程中，移除了 State 欄位，因為State欄位很多種不同的類別值，對本次預測沒有幫助。

三、特徵工程過程
類別特徵處理
原欄位
原始值
數值轉換後
International plan
Yes / No
1 / 0
Voice mail plan
Yes / No
1 / 0
Churn
True / False
1 / 0


若有做數值標準化（如StandardScaler）請列出方法與理由
對以下連續數值欄位進行Z-score標準化（均值為 0，標準差為 1）：

原因：
不同數值範圍會影響模型收斂（尤其是 Logistic Regression、KNN 等模型對距離敏感）
標準化可提升模型準確性與穩定性
依照特徵重要性篩選或保留所有欄位，並說明原因
移除欄位：State, Area code（對流失無直接預測價值）
保留原始特徵外，新增以下派生特徵以提升模型預測能力：
新增欄位
說明
many_service_calls
客服次數是否超過 3 次（流失機率高）
no_voicemail_usage
沒開語音信箱且語音留言為 0（活躍度低）
avg_call_duration_day
日通話平均時長（通話品質/偏好指標）
high_usage
日通話分鐘是否高於平均（可能為忠實用戶）



四、各模型建立細節（參數設定）
主要參數與選擇原因
模型名稱
主要參數設定
選擇原因與說明
Logistic Regression
max_iter=1000
避免預設迭代不足造成未收斂
K-Nearest Neighbors
n_neighbors=5
為常見預設值，可平衡過度與不足擬合
Random Forest
n_estimators=100, random_state=42
增加樹數以提升穩定性，設定隨機種子確保結果可重現
XGBoost
use_label_encoder=False, eval_metric='logloss', random_state=42
關閉 label encoder，指定損失函數為對數損失；效果穩定、AUC 高


在GridSearchCV中對XGBoost的 max_depth, learning_rate, subsample, n_estimators 等參數進行調整，以優化模型表現。

五、模型比較與分析
將四個模型的各指標結果整理成表格，方便比較

模型名稱
Accuracy
Precision
Recall
F1-score
ROC AUC
Logistic Regression
0.87
0.74
0.60
0.66
0.85
K-Nearest Neighbors
0.84
0.60
0.51
0.55
0.60
Random Forest
0.94
0.92
0.75
0.83
0.87
XGBoost
0.95
0.94
0.79
0.86
0.90


分析：XGBoost 表現最穩定且優異，各項指標均為最高，特別是在 recall 與 AUC 表現明顯領先；KNN 表現最弱，可能因為資料高維、類別分布不平衡導致效果不佳。

六、結論與改進建議
哪一個模型表現最好？為什麼？
XGBoost 是表現最好的模型：透過比較不同分類模型的 ROC 曲線可知，XGBoost 模型在預測客戶流失方面表現最佳，其 AUC 達到 0.90，明顯優於其他模型，且 ROC 曲線幾乎緊貼左上角，代表具有優異的預測能力與泛化效果。相較之下，KNN 僅有 0.60，接近隨機猜測，表現最差。
影響客戶流失的主要因素？
根據 Random Forest 與 XGBoost 的特徵重要性視覺化分析，客戶互動頻率與通話行為是預測流失的重要訊號。
模型優勢與限制
優勢
XGBoost 精度高，具強大泛化能力與處理非線性關係能力
自動處理缺失值，XGBoost 可容忍部分資料缺失
特徵重要性可解釋，可視覺化重要因素輔助決策
限制
訓練時間較長，GridSearch 時間成本高
過度擬合風險，對小樣本資料，需注意正規化與調參
模型較難解釋，對非技術背景使用者來說不易理解預測邏輯

未來改善方向
處理資料不平衡問題
使用 SMOTE 生成合成資料平衡流失與非流失樣本
使用 class_weight='balanced' 調整模型懲罰權重
提升模型解釋性
引入 SHAP (SHapley Additive exPlanations) 分析個別預測貢獻
製作客戶等級報表給營運端參考
特徵資料擴充
加入更多使用行為變數，如「最近一次客服聯絡內容」、「資費變更紀錄」
加入地區、族群、裝置資訊等外部資料
模型部署考量
模型需定期 retrain，以跟上用戶行為變化
建立 alert 系統：流失風險達門檻即通知客服單位介入


七、專案原始碼
github：https://github.com/meminn422/113-AIClass-Customer-_Churn_Prediction.git
google colab：https://drive.google.com/file/d/1WUCK0DkmD7gosCeTKV6BxV3yLo0zukix/view?usp=sharing
GridSearchCV：https://colab.research.google.com/drive/1s0YHZYMCGiUua165rojGPLVUNniCd6fR?usp=sharing


八、額外思考
如何因應資料不平衡問題
重新抽樣（Resampling）
上取樣（Oversampling）：使用 SMOTE（合成少數類別樣本）來增加流失客戶資料數量。
下取樣（Undersampling）：隨機刪除部分非流失客戶資料，使正負樣本更平衡。
加權模型訓練
使用模型中的 class_weight='balanced' 參數，自動對少數類別給予更高權重，讓模型更加重視流失者的預測準確性。
評估指標調整
除了 accuracy，應特別關注 recall、F1-score 與 ROC AUC 等能反映少數類別預測能力的指標。
若要部署此模型到實際服務，需要注意哪些風險或問題
資料更新與再訓練頻率
客戶行為隨時間變化，若模型長期未更新，預測準確性將逐漸下降。因此需定期 retrain（例如每月或每季）以維持效能。
即時性需求與系統效能
若模型需即時評估客戶流失風險，必須考慮推論速度與系統資源，確保在秒級內完成回應。
解釋性需求與使用者信任
企業內部使用者（如客服、行銷）可能需理解模型預測邏輯。建議搭配 SHAP 值或特徵重要性報告，提供預測背後的「理由」。
誤判成本與後果
若將未流失客戶誤判為高風險，可能會造成不必要的資源浪費或打擾。建議配合人員審核流程，降低誤判風險。
隱私與合規風險
使用包含個人行為紀錄的資料進行模型訓練，需遵守相關資料保護法規（如 GDPR），並確保客戶知情與同意。


