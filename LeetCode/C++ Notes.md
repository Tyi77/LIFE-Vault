## Library Input
```c++
#include <bits/stdc++.h>
```
## CLI Input
```c++
int main() {
	// 1 任何看不見的字符都會斷掉cin (space, \n, \t...)
	int a;
	cin >> a;
	
	// 2 接收任何 char ，space, \n, \t, \0 都可
	char ch;
	cin.get(ch);

	// 3 接收一行資料 (包含空格)，會讀入 \n 或 \0 並吃掉此字元 
	string str;
	getline(cin, str);

	return 0;
}
```

|                   | 空格  | \t  | \n  |
| :---------------: | :-: | :-: | :-: |
|    cin >> var     |  ✗  |  ✗  |  ✗  |
|    cin.get(ch)    |  ✓  |  ✓  |  ✓  |
| getline(cin, str) |  ✓  |  ✓  |  ✗  |
