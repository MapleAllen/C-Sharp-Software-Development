# C# 控制流 (Control Flow)

## 条件语句 (Conditional Statements)

### if-else
```csharp
int score = 85;
if (score >= 90) {
    Console.WriteLine("A");
} else if (score >= 80) {
    Console.WriteLine("B");
} else {
    Console.WriteLine("C");
}
```

### switch
```csharp
int day = 3;
switch (day) {
    case 1:
        Console.WriteLine("Monday");
        break;
    case 2:
        Console.WriteLine("Tuesday");
        break;
    default:
        Console.WriteLine("Another day");
        break;
}
```

## 循环语句 (Loops)

### for 循环
```csharp
for (int i = 0; i < 5; i++) {
    Console.WriteLine(i);
}
```

### foreach 循环 (常用于集合)
```csharp
string[] names = { "Alice", "Bob", "Charlie" };
foreach (string name in names) {
    Console.WriteLine(name);
}
```

### while / do-while
```csharp
int count = 0;
while (count < 3) {
    Console.WriteLine(count++);
}
```
