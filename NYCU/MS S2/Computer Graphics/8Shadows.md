- shadow types : umbra + penumbra
- apply filtering on to the aliasing
	1. Gaussian Filtering to make the edge smoother
	2. Bad way: Average the depths of the neighbors around the pixel.
	3. Good way: For each neighboring point, use the depth buffer to determine whether it is in shadow. Then, calculate the percentage of the shadowed points among all the neighbors and use the percentage value as the shadow of this pixel.
## Shadow Volume
- Use in multiple-shadowing-object scenario
- stencil buffer (refer to masked)
	- Mechanism
		- 整數來表示是否會被遮擋，加加減減後就可以區別被遮擋的程度
		- front-face: +1; back-face: -1
		- 數字越大，此區域被遮擋的程度越大
	- Effects
		- mirror effect
		- hairy effect