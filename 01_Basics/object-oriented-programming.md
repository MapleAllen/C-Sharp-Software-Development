# C# 面向对象编程 (OOP)

C# 是一门纯粹的面向对象语言。

## 类与对象 (Classes and Objects)
```csharp
public class Person {
    public string Name { get; set; } // 属性
    public int Age { get; set; }

    public void Greet() {
        Console.WriteLine($"Hello, my name is {Name}.");
    }
}

// 实例化
Person p = new Person { Name = "Alice", Age = 30 };
p.Greet();
```

## 三大特性
### 1. 封装 (Encapsulation)
通过访问修饰符控制成员的可见性：`public`, `private`, `protected`, `internal`。

### 2. 继承 (Inheritance)
```csharp
public class Employee : Person {
    public string Company { get; set; }
}
```

### 3. 多态 (Polymorphism)
- **编译时多态**: 方法重载 (Overloading)
- **运行时多态**: 方法重写 (Overriding, 使用 `virtual` 和 `override` 关键字)

## 接口 (Interfaces)
定义了一组约定的行为。
```csharp
public interface IShape {
    double GetArea();
}

public class Circle : IShape {
    public double Radius { get; set; }
    public double GetArea() => Math.PI * Radius * Radius;
}
```
