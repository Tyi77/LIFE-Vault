## Hello World
```kotlin
fun main() {
    // ===
    println("Hello World!")
    val vs var // Read-only vs Mutable
    // ===
    val number = 10
    println("We got $number colas")
    println("We got $(number + 1) colas")
    // ===
}
```
## Basic Type
| **Category**           | **Basic types**                    | **Example code**                                              |
| ---------------------- | ---------------------------------- | ------------------------------------------------------------- |
| Integers               | `Byte`, `Short`, `Int`, `Long`     | `val year: Int = 2020`                                        |
| Unsigned integers      | `UByte`, `UShort`, `UInt`, `ULong` | `val score: UInt = 100u`                                      |
| Floating-point numbers | `Float`, `Double`                  | `val currentTemp: Float = 24.5f`, `val price: Double = 19.99` |
| Booleans               | `Boolean`                          | `val isEnabled: Boolean = true`                               |
| Characters             | `Char`                             | `val separator: Char = ','`                                   |
| Strings                | `String`                           | `val message: String = "Hello, world!"`                       |

---
## Collections
### List
```Kotlin
// == Read-only vs Mutable ==
// Read only list
val readOnlyShapes = listOf("triangle", "square", "circle")
println(readOnlyShapes)
// [triangle, square, circle]

// Mutable list with explicit type declaration
val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
println(shapes)
// [triangle, square, circle]

// == Some Operations ==
val readOnlyShapes = listOf("triangle", "square", "circle")
println(readOnlyShapes[0]) // triangle
println(readOnlyShapes.first()) // triangle
println(readOnlyShapes.last()) // circle
println(readOnlyShapes.count()) // 3
println("circle" in readOnlyShapes) // true

val shapes: val shapes: MutableList<String> = mutableListOf("triangle", "square", "circle")
shapes.add("rectengle") // 加在後面
shapes.add(1, "rectengle") // 加在 index 1 那裡
```
### Set
- 一樣有read-only 和 mutable 兩種版本
- 因為集合沒有位置的概念，所以沒有辦法直接用index access
- 有 in 語法
- 有 `.add() .remove() .count()`
### Map
- 就是c++的map
- 用 to 語法連結 key 和 value (e.g. `("apple" to 100, "kiwi" to 10)` )
- 加東西直接 `mapName["banana"] = 50`
- `.remove(keyName) .count()`
- `.containKey(keyName)`來確定是否有此 key 的出現，true or false
- 用 `mapName.keys` 或 `mapName.values` 來獲取 key 或 value 的集合
	- 因為 keys 和 values 是個 collection，所以可以用 in 語法來確認 key 或 value 是否存在在 map 中
---
