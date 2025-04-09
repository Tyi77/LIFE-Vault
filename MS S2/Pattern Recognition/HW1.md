## 步驟
- 選擇分類模型
	- 多重感知機
	- 貝氏分類器
- 選擇evaluation的模型
## 1. **Ionosphere**（二分類）

- **資料特性**：351 筆樣本，34 維特徵，全數值型、平衡
    
- **推薦分類器**：
    
    - `Gaussian Naïve Bayes`（高維時仍有穩定表現）
        
- **評估方式**：
    
    - `Confusion Matrix`：2x2
        
    - `ROC Curve` 與 `AUC`：因為是二分類，非常適合使用
        

---

## 2. **Breast Cancer Wisconsin (Diagnostic)**（二分類）

- **資料特性**：569 筆樣本，30 維特徵，平衡
    
- **推薦分類器**：
    
    - `Logistic Regression`（線性分類器，適合中等維度）
        
- **評估方式**：
    
    - `Confusion Matrix`
        
    - `ROC Curve` / `AUC`
        
    - 可以額外做 threshold 調整實驗觀察 sensitivity vs. specificity
        

---

## 3. **Iris**（三分類）

- **資料特性**：150 筆樣本，4 維特徵，3 類、完全平衡
    
- **推薦分類器**：
    
    - `K-Nearest Neighbors`（KNN）：小資料集，計算量低，效果穩定
        
    - 使用 K=1、3、5 測試影響
        
- **評估方式**：
    
    - `Confusion Matrix`（3x3）
        
    - 每類 precision/recall
        
    - `Accuracy`
        

---

## 4. **Wine**（三分類）

- **資料特性**：178 筆樣本，13 維特徵，3 類、近乎平衡
    
- **推薦分類器**：
    
    - `Multiclass Linear Classifier`（如感知機 or softmax regression）
        
    - 或改進版 `Naïve Bayes`（可做類別-特徵分析）
        
- **評估方式**：
    
    - `Confusion Matrix`（3x3）
        
    - 每類 precision/recall
        
    - 可以額外做 feature subset 實驗
        

---

## 🔍 延伸建議：

- 所有資料都可以：
    
    - 嘗試「只用部分特徵」做 feature selection 測試
        
    - 用「不同資料量」觀察分類表現穩定性（learning curve）
        