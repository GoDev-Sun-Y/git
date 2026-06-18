
### 一、打印在控制台
```go
1. fmt.Print( )
2. fmt.Println( )
3. fmt.Printf( )
```
注意：fmt.Printf是格式化输出，需要配合格式字符串使用（如 fmt.Printf("%d", 123)）。

### 二、以返回字符串的方式使用
```go
1. s := fmt.Sprint( )
2. s := fmt.Sprintf( )
3. s := fmt.Sprintln( )
```
### 三、以读取的方式使用
```go
1. fmt.Scan(&变量名)
2. fmt.Scanln(&变量名)
3. fmt.Scanf("%d", &变量名)   // 举例：读取整数
```
fmt.Scan：读取空格/换行分隔的值，不自动换行。

fmt.Scanln：读取一行，遇到换行停止，自动换行。

fmt.Scanf：按格式读取，必须匹配格式，如 %d、%s等。

 ### 四、格式化占位符部分
```go
%v   任意类型（默认格式
%p   指针
%T   类型
%c   Unicode 字符（rune）
%d   整形
%f   浮点数（默认6位小数）
%.nf 保留n位精度浮点形
%s   字符串
%t   布尔
```

# Go 变量、常量与随机数声明整理

## 一、变量声明（var）

### 1. 显式类型 + 初始化

```go
var 变量名 类型 = 值
```

### 2. 仅声明，未初始化

```go
var 变量名 类型
```

> ⚠️ 全局变量合法，局部变量需后续赋值，否则编译报错。

### 3. 类型推断 + 初始化（推荐）

```go
var 变量名 = 值
```

### 4. 短变量声明（仅限函数内）

```go
变量名 := 值
```

> ⚠️ `:=` 只能用于函数内，不能用于全局变量。

### 5. 多变量声明（同一类型）

```go
var 变量名1, 变量名2 类型
```

或

```go
var 变量名1, 变量名2 = 值1, 值2
```

> ⚠️ 多变量声明必须指定类型或提供初始值，不能只写 `var 变量名1, 变量名2`。

### 6. 多变量声明（不同类型）

```go
var (
    变量名1 类型1
    变量名2 类型2
)
```

> ⚠️ 括号内可声明多个不同类型的变量，每个变量需独立声明类型。

---

## 二、常量声明（const）

### 1. 显式类型 + 初始化

```go
const 常量名 类型 = 值
```

### 2. 类型推断 + 初始化

```go
const 常量名 = 值
```

### 3. 批量声明

```go
const (
    常量名1 = 值1
    常量名2 = 值2
)
```

> ⚠️ 注意事项：
> - 常量必须初始化，不能只声明不赋值。
> - 不能用 `:=`。
> - 常量在编译期确定，运行时不可变。

---

## 三、随机数生成

### 1. `rand.Intn(n)` 用法

```go
变量名 := rand.Intn(n)
// 随机生成一个 [0, n) 范围内的整数（包含 0，不包含 n）
```

> 💡 需要先导入：`import "math/rand"` 和 `import "time"`（用于种子）

### 2. 独立随机数生成器（避免全局种子冲突）

```go
变量名 := rand.New(rand.NewSource(time.Now().UnixNano()))
// 定义独立的随机数生成器，适合并发或需要独立随机序列的场景
```

> 使用示例：
>
> ```go
> r := rand.New(rand.NewSource(time.Now().UnixNano()))
> num := r.Intn(100) // 生成 0~99 的随机数
> ```

---

# 分支结构 
## if-else结构

### 1. 单 if 语句


```go
if 条件 {
    // 满足条件时执行
}
```

> 💡 注：这里的"条件"本身就是一个布尔表达式，如 `x > 5`。

---

### 2. if-else

```go
if 条件 {
    // 满足时执行
} else {
    // 不满足时执行
}
```

---

### 3. if-else if

```go
if 条件1 {
    // 满足条件1时执行
} else if 条件2 {
    // 不满足条件1，但满足条件2时执行
}
```

---

## switch 结构

### 原稿

```go
switch 值 {
case 条件语句 :
case :
default :
}
```

```go
switch 值 {
case 值1:
    // 匹配值1时执行
case 值2:
    // 匹配值2时执行
default:
    // 不匹配任何case时执行
}
```

### 扩展：条件表达式 switch（Go 1.22+ 支持）

> 如果想像 `if-else if` 一样写条件判断，可以省略 `switch` 后面的值：

```go
switch {
case x > 5:
    // x > 5 时执行
case x == 3:
    // x == 3 时执行
default:
    // 以上都不满足时执行
}
```

---

## 四、switch fallthrough 语法

```go
switch 值 {
case 值1:
    // 执行语句
    fallthrough // 强制执行下一个case
case 值2:
    // 即使不满足值2，也会执行这里
default:
    // ...
}
```

## 目录

- [一、strconv 包](#一strconv-包)
- [二、常用 strings 包](#二常用-strings-包)
- [三、函数基本格式](#三函数基本格式)
- [附录：错误清单速查表](#附录错误清单速查表)

---

# 一、strconv 包

### 1.1 字符串转整数


```go
num, err := strconv.Atoi("123")
if err != nil {
    // 处理错误
}
fmt.Println(num) // 123
```


### 1.2 字符串转浮点数

```go
f, err := strconv.ParseFloat("3.14", 64)
if err != nil {
    // 处理错误
}
fmt.Println(f) // 3.14
```

---

## 二、常用 strings 包

### 2.1 包含关系判断
```go
strings.Contains("hello", "ell") // true
```

---

### 2.2 重复字符串


```go
strings.Repeat("ab", 3) // "ababab"
```

---

### 2.3 字符串切片转字符串

```go
result := strings.Join([]string{"a", "b", "c"}, "-") // "a-b-c"
```

### 2.4 替换字符串

```go
result := strings.Replace("hello", "l", "x", 2) // "hexxo"
// n = -1 表示替换所有匹配项
result = strings.Replace("hello", "l", "x", -1) // "hexxo"
```
---

### 2.5 替换全部

```go
result := strings.ReplaceAll("hello", "l", "x") // "hexxo"
```

> 💡 等价于 `strings.Replace(s, old, new, -1)`

---

## 三、函数基本格式

### 3.1 基本语法

```go
// 单返回值
func add(a int, b int) int {
    return a + b
}

// 多返回值
func divide(a int, b int) (int, error) {
    if b == 0 {
        return 0, errors.New("除数不能为 0")
    }
    return a / b, nil
}
```
# 数组（Array）

### 1.1 声明与初始化

```go
// ① 声明固定长度数组（未初始化，元素为零值）
var arr [5]int

// ② 声明并初始化数组
var arr = [5]int{1, 2, 3, 4, 5}

// ③ 短变量声明（自动类型推断）
arr := [5]int{1, 2, 3, 4, 5}

// ④ 让编译器自动推断长度
arr := [...]int{1, 2, 3, 4, 5}

// ⑤ 元素少于长度时，其余补零
arr := [5]int{1, 2} // 结果：[1, 2, 0, 0, 0]
```

---


### 1.2 访问数组元素

```go
// 取值
arr[0]

// 赋值
arr[0] = 100
```

---

## 二、for range 循环

### 2.1 三种用法

#### ✅ 正确语法

```go
// ① 只循环，不获取索引和值（用空白标识符 _）
for _ = range arr {
    // 仅循环，不关心索引和值
}

// ② 只获取索引
for i := range arr {
    fmt.Println(i, arr[i])
}

// ③ 同时获取索引和值
for i, v := range arr {
    fmt.Println(i, v)
}
```

---

## 三、切片（Slice）

### 3.1 声明与初始化
```go
// ① 声明 nil 切片（未初始化，值为 nil）
var s []int

// ② 声明并初始化切片（推荐）
s := []int{1, 2, 3}

// ③ 使用 make 创建（指定长度）
s := make([]int, 5)      // 长度=5，容量=5

// ④ 使用 make 创建（指定长度和容量）
s := make([]int, 3, 5)   // 长度=3，容量=5
```

### 3.2 访问切片元素

```go
// 取值
s[0]

// 赋值
s[0] = 100
```

---

## 四、数组 vs 切片 对比

| 特性 | 数组（Array） | 切片（Slice） |
|------|--------------|--------------|
| 声明方式 | `var arr [5]int` | `var s []int` |
| 长度 | **固定**，是类型的一部分 | **可变**，可动态增长 |
| 传递方式 | 值传递（拷贝整个数组） | 引用传递（底层共享数组） |
| 初始化 | `[5]int{1,2,3,4,5}` | `[]int{1,2,3}` 或 `make([]int, 5)` |
| 零值 | 所有元素为零值 | `nil` |

---

## 五、常见错误总结

| 错误 | 说明 | 正确写法 |
|------|------|---------|
| `for range arr { }` | ❌ 编译错误 | `for _ = range arr { }` |
| `make([]int, 长度(容量))` | ❌ 中文括号 | `make([]int, 3, 5)` |
| 把数组当切片用 | ❌ 类型不匹配 | 数组需显式转换为切片：`arr[:]` |
| `var s []int` 直接访问 `s[0]` | ❌ panic: index out of range | 需先初始化：`s = append(s, 0)` 或 `s := make([]int, 1)` |

---

> 📝 **学习建议**：手写笔记是很好的学习方式，但 Go 语言的语法细节较多，建议配合 [Go 官方教程](https://go.dev/tour/) 和《Go 语言圣经》一起学习，效果更佳！


# Go 语言常用语法速查笔记


## 一、`append` 语法

### 基本用法

```go
切片名 = append(切片名, 元素1, 元素2, ...)   // 追加多个元素
切片名 = append(切片名, 另一个切片...)         // 展开切片追加
```

### ⚠️ 注意事项

- `append` **可能重新分配底层数组**，因此**必须接收返回值**（即使容量足够）。
- `元素...` 是可变参数，可以传多个独立元素，也可以传一个切片（需加 `...` 展开）。

---

## 二、`copy` 语法

```go
复制元素数量 := copy(目标切片, 源切片)
```

### ⚠️ 注意事项

- 返回值是**实际复制的元素个数**，取 `min(len(dst), len(src))`。
- `dst` 和 `src` 必须是**同类型切片**。

---

## 三、`for` 循环

| 形式 | 说明 | 示例 |
|------|------|------|
| `for { }` | 无限循环 | `for { fmt.Println("loop") }` |
| `for 条件 { }` | 条件循环 | `for i < 10 { fmt.Println(i); i++ }` |
| `for i := 0; i < n; i++ { }` | 传统 for 循环 | `for i := 0; i < 10; i++ { fmt.Println(i) }` |

---

## 四、`map` 声明方式

| 方式 | 语法 | 说明 |
|------|------|------|
| ① 声明 | `var 映射名 map[KeyType]ValueType` | 仅声明，未初始化，**不能直接使用**（会 panic），需后续 `make` 或赋值 |
| ② 字面量 | `映射名 := map[KeyType]ValueType{键: 值}` | 字面量初始化，可直接使用 |
| ③ make | `映射名 := make(map[KeyType]ValueType)` | `make` 创建，推荐用于动态添加 |

---

## 五、`map` 调用方式

```go
值 := 映射名[键名]              // 键不存在时，返回值类型的零值
值, 存在 := 映射名[键名]        // 存在 为 bool，表示该键是否存在
```

### 示例

```go
v, ok := m["key"]
if ok {
    fmt.Println("存在:", v)
} else {
    fmt.Println("不存在")
}
```

---

## 六、`delete` 语法

```go
delete(映射名, 键名)
```

### ⚠️ 注意事项

- 删除**不存在的键不会报错**，属于安全操作。

---

## 七、排序

### 整型切片排序

```go
sort.Ints(切片名)   // 升序排列，仅适用于 []int
```

### 通用切片排序（`sort.Slice`）

```go
// 按字符串长度升序（短的在前）
sort.Slice(切片名, func(i, j int) bool {
    return len(切片名[i]) < len(切片名[j])
})
```

```go
// 按字符串字典序升序
sort.Slice(切片名, func(i, j int) bool {
    return 切片名[i] < 切片名[j]
})
```

### ⚠️ 注意


- 该写法**仅适用于元素为 `string` / `slice` / `array` 等可求长度的类型**，并非通用。

---

# 冒泡排序
```go
for i := 0; i < len(切片名); i++ {
    for j := 0; j < len(切片名)-1-i; j++ {
        if 切片名[j] > 切片名[j+1] {
            切片名[j], 切片名[j+1] = 切片名[j+1], 切片名[j] // 交换相邻元素
        }
    }
}
```
