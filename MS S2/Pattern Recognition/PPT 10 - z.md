- **兩類分類**問題，符合這個內容就代表在高維是線性可分割的![[Pasted image 20250604210430.png]]![[Pasted image 20250604210841.png]]
	- $\varphi(x)$ : 高維映射函數
	- w : hyperplane (分開不同類別的分割平面)
	- $\omega$ : 不同類別
## Radial Basis Functions (RBF)
- $\varphi(x)$ 的一些範例![[Pasted image 20250604211712.png]]
- 整個網路架構+數學通式![[Pasted image 20250604213632.png]]
## SVM
### Margin
- Linear Discriminant Function ( Decision Boundary $g(x)=0$ )![[Pasted image 20250605161847.png]]
- 定義 Margin 的邊界![[Pasted image 20250605164950.png]]
- Margin 的寬度。 Optimization目標![[Pasted image 20250605165029.png]]
### Optimization
- Optimization Target ( 直觀上就是：「在維持所有資料點至少離邊界 1 單位的前提下，把 ∥w∥ 盡量搞小，讓中間那條空白地帶（margin）變廣。」 )![[Pasted image 20250605165803.png]]
- 這個才是真正要被Optimization的式子![[Pasted image 20250605170913.png]]
	- $\lambda_i$ : Lagrange multipliers 拉格朗日乘子![[Pasted image 20250605171046.png]]
	- $y_i$ : 讓左右判別值都變成正值。
- 最終結果，用此結果分類新的數據點![[Pasted image 20250605170938.png]]![[Pasted image 20250605170943.png]]
### Non-separable Cases
