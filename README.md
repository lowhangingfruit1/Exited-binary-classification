# Exited-binary-classification
Exited binary classification

共嘗試5次，分別是
[01] RandomForestClassifier
[02] RandomForest調整樣本權重--classification
[03] XGBoost-classification
[04] 使用 RandomizedSearchCV 調整 XGBoost 參數-classification
[05] 使用XGBoost_資料清理和特徵工程調參_根據正負樣本比例設置範圍-classification目前以 [04]版本為佳，使用 RandomizedSearchCV 調整 XGBoost 參數-classification，請看line 77
檔案請見file資料夾，code請見code202411資料夾

[01] RandomForestClassifier
F1 Score: 0.6030035335689046
Confusion Matrix:
 [25100   952]
 [ 3542  3413] 
![image](https://github.com/user-attachments/assets/4cc36e3d-f55d-4f57-a81c-e8d3ea649c4b)
![image](https://github.com/user-attachments/assets/372a7e37-af94-4957-8cb3-e313db6b1f27)
1. F1 Score: 0.603
F1 Score 是精確率（Precision）和召回率（Recall）的調和平均數。值越接近 1，表示模型在不平衡數據集上的表現越好。此處 F1 Score 為 0.603，表明模型在識別離職（Exited=1）方面有中等的準確性，但也暗示著模型在某些情況下可能無法完全正確地預測離職情況。這通常表示正類（離職）的精確度或召回率之間存在權衡。
2. Confusion Matrix 分析
混淆矩陣顯示模型在不同類別上的表現：
•	True Negatives (TN)：25100，模型正確預測了 25100 名未離職的員工。
•	False Positives (FP)：952，模型錯誤地預測 952 名未離職員工為離職員工。
•	False Negatives (FN)：3542，模型錯誤地將 3542 名實際離職員工預測為未離職。
•	True Positives (TP)：3413，模型正確預測了 3413 名離職員工。
可以看出：
•	模型對未離職員工的預測準確度較高，因為 True Negatives (25100) 相較於 False Positives (952) 的數量大。
•	模型在離職員工預測方面表現較差，False Negatives (3542) 數量顯示模型錯過了部分離職員工，這可能導致離職員工的召回率不高。
3. ROC Curve 和 AUC 分析
ROC 曲線顯示了模型在不同閾值下的預測能力。該曲線衡量的是**True Positive Rate（真正率）與False Positive Rate（假正率）**之間的關係。
•	AUC 值為 0.89 表示模型具有不錯的分辨能力。AUC 越接近 1 表示模型的分類效果越好。在這個例子中，AUC 為 0.89，顯示出模型在離職與未離職分類上有較高的區分度。
結論
•	模型在識別未離職員工（Negative Class）方面表現較好，這從混淆矩陣中的 True Negatives 大於 False Positives 可以看出。
•	模型在識別離職員工（Positive Class）方面有一定挑戰，因為存在較多的 False Negatives，這也反映在中等的 F1 Score 上。
•	ROC 曲線和 AUC 值表明模型具有良好的整體分辨能力，但模型的精確率和召回率之間可能存在一些權衡。


[02] RandomForest調整樣本權重--classification
RandomForestClassifier 中，可以使用 class_weight='balanced'，根據類別樣本數自動調整樣本權重，使模型更關注少數類別。
F1 Score: 0.5995241871530531
Confusion Matrix:
 [25060   992]
 [ 3553  3402]
![image](https://github.com/user-attachments/assets/aeedcd10-cd44-47ae-bc09-1cee034c6732)
![image](https://github.com/user-attachments/assets/176639b6-6ec5-4ccf-98ef-22dd0664e8c3)
相比之前的 F1 Score 0.603 稍微下降了一些，現在的 F1 Score 是 0.599。這意味著模型的整體平衡性能有所減少。
混淆矩陣對比
新的混淆矩陣與之前的比較：
•	True Negatives (TN)：從 25100 降為 25060，少了 40 個正確預測的未離職員工。
•	False Positives (FP)：從 952 增加到 992，錯誤預測的未離職員工增加了 40 個。
•	False Negatives (FN)：從 3542 增加到 3553，錯誤預測的離職員工增加了 11 個。
•	True Positives (TP)：從 3413 降至 3402，正確預測的離職員工減少了 11 個。


[03] XGBoost-classification
F1 Score: 0.6442645736243969
Confusion Matrix:
 [24716  1336]
 [ 3015  3940]
![image](https://github.com/user-attachments/assets/6d1caf6b-79ac-4c07-b3ec-de85676996c9)
![image](https://github.com/user-attachments/assets/83373127-6ba0-4182-a326-55b70aeae74c)
•	混淆矩陣變化：
o	True Positives (TP) 增加：從 3413 提升至 3940，這意味著模型更好地識別出離職員工。
o	False Positives (FP) 增加了一些，但 True Positives 的增加更為顯著，導致整體的 F1 Score 提升。
o	False Negatives (FN) 減少：從 3542 降到 3015，模型的召回率（對離職員工的檢出率）有所提升。
改善原因
1.	XGBoost 的優勢：
o	XGBoost 是一種強大的梯度提升算法，具有更高的模型擬合能力和靈活性，特別是在非線性數據或特徵交互較強的數據中表現較好。
o	XGBoost 能夠處理特徵的重要性並自動加權，這可以提高模型在少數類別（如離職員工）上的識別能力。
2.	隨機森林與 XGBoost 的區別：
o	隨機森林是通過多棵決策樹的投票來做預測，但每棵樹之間是獨立的。
o	XGBoost 使用梯度提升，樹之間是逐步構建的，每棵樹的目的是糾正前一棵樹的錯誤。這樣的過程可以更好地最小化誤差，尤其在較複雜的數據上更具優勢。
3.	F1 Score 提升的原因：
o	XGBoost 更好地平衡了精確率和召回率，特別是在樣本不平衡的情況下，對少數類別（Positive Class，即離職）的召回率改善顯著，這直接影響了 F1 Score 的提升。

[04] 使用 RandomizedSearchCV 調整 XGBoost 參數-classification
缺點: 耗時較長
F1 Score: 0.6726085181657367
Confusion Matrix:
 [23252  2800]
 [ 2012  4943]

 Best Parameters: {'colsample_bytree': 0.9878338511058234, 'learning_rate': 0.16502656467222293, 'max_depth': 4, 'n_estimators': 251, 'scale_pos_weight': 2, 'subsample': 0.9706635463175177}
![image](https://github.com/user-attachments/assets/33470283-b787-470c-8195-0e4804ef14b6)
![image](https://github.com/user-attachments/assets/80547045-d069-4142-a55b-94098204f59e)

•	F1 Score：0.6726，明顯高於之前的 0.644（未經調整參數的模型）。
•	混淆矩陣：
o	True Positives (TP)：4943（模型正確識別的離職員工數量）。
o	False Positives (FP)：2800（模型錯誤地預測為離職的未離職員工數量）。
o	False Negatives (FN)：2012（模型錯誤地預測為未離職的離職員工數量）。
o	True Negatives (TN)：23252（模型正確識別的未離職員工數量）。
結果解釋
•	F1 Score 提升：調整後的 F1 Score 提高到 0.6726，這顯示出模型對於離職員工的識別能力有所提升，平衡了精確率（Precision）和召回率（Recall）。
•	AUC 提升：ROC 曲線下的 AUC 值為 0.90，意味著模型在區分離職和未離職員工上有了更高的準確性。
•	減少 False Negatives：相較於之前，False Negatives（即錯誤分類的離職員工）數量減少了，使得模型的召回率提升，這對 F1 Score 的提升貢獻很大。
調整參數的效果
•	learning_rate：調整到 0.1650，這個略高的學習率讓模型在適當的速度下學習，避免了過度平滑。
•	max_depth：設定為 4，適中的深度幫助模型避免過擬合，同時有效擬合數據。
•	n_estimators：增加到 251，使模型有更多的樹來學習特徵。
•	scale_pos_weight：設置為 2，有效地平衡了不平衡的類別，使少數類別（離職員工）更受重視。
總結
•	使用 RandomizedSearchCV 調整參數後，XGBoost 模型的 F1 Score 和 AUC 明顯提升，這表明模型在區分離職和未離職員工方面的表現更佳。


[05] 使用XGBoost_資料清理和特徵工程調參_根據正負樣本比例設置範圍-classification
資料清理和特徵工程，進行的修改如下：
1.檔案名稱為cleaned_train_data.xlsx
2.移除無用欄位：
3. 對 CreditScore、Age、Balance 和 EstimatedSalary 這些數值特徵進行了 Z-score 標準化，以幫助模型更有效地處理數據範圍的差異。
類別變數 One-Hot Encoding：
4.對 Geography 和 Gender 進行了 One-Hot Encoding，新增了 Geography_Germany、Geography_Spain 和 Gender_Male 三個欄位，並移除了原始的文字欄位。
完成的資料集現在包含12個欄位，其中數值欄位已標準化，類別變數已進行編碼
5. scale_pos_weight 設置建議
•  正樣本數量 = 34,921
•  負樣本數量 = 165,034 - 34,921 = 130,113
正負樣本比例=130,113/34,921≈3.72
根據這個比例，將 scale_pos_weight 初始設為 3.72，或在其附近範圍（例如 3 到 5 之間）進行調整和微調，以找出最佳效果。這樣的設置可幫助模型更好地平衡正負樣本。

F1 Score: 0.6517449747194475
Confusion Matrix:
 [[22074  3978]
 [ 1670  5285]]
Best Parameters: {'colsample_bytree': 0.8365191150830908, 'learning_rate': 0.06494435859801283, 'max_depth': 5, 'n_estimators': 242, 'scale_pos_weight': 3, 'subsample': 0.6478376983753207}
![image](https://github.com/user-attachments/assets/fb5dc167-4ba1-4a78-817d-e7992c44715b)
![image](https://github.com/user-attachments/assets/a239417e-9c8b-48d3-91ce-d6d8045a0058)




 
