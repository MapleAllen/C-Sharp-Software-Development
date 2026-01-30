# C# 基础语法 (Basic Syntax)

C# 是一种强类型、面向对象的编程语言。

## 变量与常量
### 变量 (Variables)
```csharp
int age = 25;
double price = 19.99;
string name = "Antigravity";
bool isActive = true;

// 使用 var 进行类型推断
var message = "Hello, C#!"; 
```

### 常量 (Constants)
```csharp
const double Pi = 3.14159;
```

## 数据类型 (Data Types)
- **值类型 (Value Types)**: `int`, `float`, `double`, `char`, `bool`, `struct`, `enum`
- **引用类型 (Reference Types)**: `string`, `class`, `interface`, `delegate`, `object`

## 运算符 (Operators)
- **算术运算符**: `+`, `-`, `*`, `/`, `%`
- **关系运算符**: `==`, `!=`, `>`, `<`, `>=`, `<=`
- **逻辑运算符**: `&&`, `||`, `!`

## 注释 (Comments)
```csharp
// 单行注释

/*
   多行注释
*/

/// <summary>
/// XML 文档注释，用于描述类或方法
/// </summary>
```
