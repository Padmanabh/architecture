# Design Patterns and Principles (SOLID, GRASP)

## Introduction

Building robust, maintainable, and scalable software systems requires more than just knowing a programming language or an architectural style. It demands a deep understanding of fundamental design principles and recurring solutions to common problems. This session will delve into Design Principles, specifically SOLID and GRASP, and then introduce you to some essential Design Patterns.

### What are Design Principles?

Design principles are guidelines that help you design effective object-oriented systems. They are fundamental truths or guiding philosophies that lead to good software design. They focus on making code more flexible, maintainable, reusable, and understandable.

### What are Design Patterns?

Design patterns are generalized, reusable solutions to commonly occurring problems in software design. They are not ready-to-use pieces of code but rather templates that you can adapt to solve a recurring design problem in your own code base. Patterns are usually more specific than principles and often implement principles.

### Why are they important for Architects?

-   **Improved Code Quality:** Applying these principles and patterns leads to cleaner, more organized, and less error-prone code.
-   **Maintainability and Extensibility:** Systems built with good design are easier to maintain, modify, and extend over time.
-   **Reduced Complexity:** They help manage the complexity of large software systems by providing structured approaches to common challenges.
-   **Enhanced Communication:** Like architectural styles, they provide a common vocabulary for development teams.
-   **Higher Reusability:** Well-designed components and modules are more likely to be reusable in other parts of the system or in future projects.

## SOLID Principles

SOLID is an acronym for five design principles intended to make software designs more understandable, flexible, and maintainable. Coined by Robert C. Martin (Uncle Bob), these principles are pillars of object-oriented design.

### 1. Single Responsibility Principle (SRP)

**Definition:** A class should have only one reason to change, meaning it should only have one job or responsibility.

**Explanation:** This principle advocates for highly cohesive classes. If a class has multiple responsibilities, changes to one responsibility might inadvertently affect another, leading to bugs and making the code harder to modify. Separating responsibilities into different classes makes the system more robust and easier to understand.

**C# Example (Violation):**

```csharp
public class Order
{
    public void AddItem(string item)
    {
        // Logic to add an item to the order
    }

    public void SaveToDatabase()
    {
        // Logic to save the order to a database
    }

    public void PrintOrder()
    {
        // Logic to print the order details
    }
}
```

In this example, the `Order` class has three responsibilities: managing order items, saving to a database, and printing. If the printing logic changes, the `Order` class needs to be modified, even though the core order management logic hasn't changed.

**C# Example (Adhering to SRP):**

```csharp
// Responsibility 1: Manage Order items
public class Order
{
    public List<string> Items { get; private set; } = new List<string>();

    public void AddItem(string item)
    {
        // Logic to add an item to the order
        Items.Add(item);
    }
}

// Responsibility 2: Persist Order
public class OrderRepository
{
    public void Save(Order order)
    {
        // Logic to save the order to a database
        Console.WriteLine($"Saving order with {order.Items.Count} items to database.");
    }
}

// Responsibility 3: Print Order
public class OrderPrinter
{
    public void Print(Order order)
    {
        // Logic to print the order details
        Console.WriteLine("\n--- Order Details ---");
        foreach (var item in order.Items)
        {
            Console.WriteLine($"- {item}");
        }
        Console.WriteLine("---------------------");
    }
}
```

Now, each class has a single, well-defined responsibility. Changes to persistence logic won't affect order management or printing, and vice-versa.

### 2. Open/Closed Principle (OCP)

**Definition:** Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification.

**Explanation:** This principle promotes code that can be extended with new functionality without altering its existing source code. This is typically achieved through abstraction (interfaces or abstract classes) and polymorphism. When you need to add new behavior, you create a new implementation of the abstraction rather than changing existing code.

**C# Example (Violation):**

```csharp
public class InvoiceCalculator
{
    public double CalculateInvoiceAmount(double amount, string customerType)
    {
        if (customerType == "Regular")
        {
            return amount * 1.10; // 10% tax
        }
        else if (customerType == "Premium")
        {
            return amount * 1.05; // 5% tax for premium
        }
        return amount; // No tax
    }
}
```

If a new customer type is introduced, you have to modify the `CalculateInvoiceAmount` method, violating OCP.

**C# Example (Adhering to OCP):**

```csharp
public interface ICustomerDiscount
{
    double ApplyDiscount(double amount);
}

public class RegularCustomerDiscount : ICustomerDiscount
{
    public double ApplyDiscount(double amount)
    {
        return amount * 0.10; // 10% discount
    }
}

public class PremiumCustomerDiscount : ICustomerDiscount
{
    public double ApplyDiscount(double amount)
    {
        return amount * 0.20; // 20% discount
    }
}

// New discount policy can be added without modifying existing classes
public class GoldCustomerDiscount : ICustomerDiscount
{
    public double ApplyDiscount(double amount)
    {
        return amount * 0.30; // 30% discount
    }
}

public class InvoiceCalculatorOCP
{
    public double CalculateFinalAmount(double baseAmount, ICustomerDiscount discountStrategy)
    {
        return baseAmount - discountStrategy.ApplyDiscount(baseAmount);
    }
}
```

Now, to add a new `GoldCustomerDiscount`, you simply create a new class implementing `ICustomerDiscount` without touching `InvoiceCalculatorOCP`.

### 3. Liskov Substitution Principle (LSP)

**Definition:** Subtypes must be substitutable for their base types without altering the correctness of the program.

**Explanation:** This principle ensures that a derived class can replace its base class in any context without causing errors or unexpected behavior. It's about behavioral subtyping. If class `S` is a subtype of class `T`, then objects of type `T` can be replaced with objects of type `S` without breaking the application.

**C# Example (Violation - classic Rectangle/Square problem):**

```csharp
public class Rectangle
{
    public virtual int Width { get; set; }
    public virtual int Height { get; set; }

    public int GetArea()
    {
        return Width * Height;
    }
}

public class Square : Rectangle
{
    public override int Width
    {
        set { base.Width = value; base.Height = value; }
    }

    public override int Height
    {
        set { base.Width = value; base.Height = value; }
    }
}

public class AreaCalculator
{
    public void CalculateAndPrintArea(Rectangle rectangle)
    {
        rectangle.Width = 5;
        rectangle.Height = 4;
        Console.WriteLine($"Area: {rectangle.GetArea()}");
    }
}

// Usage that would break with Square
// var square = new Square(); // If you pass a Square to CalculateAndPrintArea, it will behave unexpectedly.
// new AreaCalculator().CalculateAndPrintArea(square); // Expected 20, but due to Square's setters, it will be 16 or 25.
```

When `Square` is substituted for `Rectangle`, the `CalculateAndPrintArea` method (which expects independent `Width` and `Height` setting) behaves unexpectedly because `Square`'s setters are coupled.

**C# Example (Adhering to LSP - separate types):**

One way to adhere to LSP here is to avoid the inheritance relationship if the subtype fundamentally changes the behavior assumed by the base class clients. `Square` is not a `Rectangle` in all behavioral aspects.

```csharp
public interface IShapeWithArea
{
    int GetArea();
}

public class RectangleLSP : IShapeWithArea
{
    public int Width { get; set; }
    public int Height { get; set; }

    public RectangleLSP(int width, int height)
    {
        Width = width;
        Height = height;
    }

    public int GetArea()
    {
        return Width * Height;
    }
}

public class SquareLSP : IShapeWithArea
{
    public int Size { get; set; }

    public SquareLSP(int size)
    {
        Size = size;
    }

    public int GetArea()
    {
        return Size * Size;
    }
}

public class NewAreaCalculator
{
    public void CalculateAndPrintArea(IShapeWithArea shape)
    {
        // We only rely on GetArea(), which is properly implemented by both types
        Console.WriteLine($"Area: {shape.GetArea()}");
    }
}

// Usage
// var rectangle = new RectangleLSP(5, 4); // Area: 20
// new NewAreaCalculator().CalculateAndPrintArea(rectangle);
// var square = new SquareLSP(4); // Area: 16
// new NewAreaCalculator().CalculateAndPrintArea(square);
```

By using an interface that only exposes `GetArea()`, `NewAreaCalculator` does not make assumptions about `Width` and `Height` setters, thus allowing `RectangleLSP` and `SquareLSP` to be substitutable.

### 4. Interface Segregation Principle (ISP)

**Definition:** Clients should not be forced to depend on interfaces they do not use. Rather than one large interface, many specific interfaces are preferred.

**Explanation:** This principle states that it's better to have many small, client-specific interfaces than one large, general-purpose interface. When an interface contains methods that some of its implementers don't need, those implementers are forced to provide empty or default implementations, which indicates a violation of ISP. This leads to fat interfaces and tight coupling.

**C# Example (Violation):**

```csharp
public interface IWorker
{
    void Work();
    void Eat();
    void Sleep();
}

public class HumanWorker : IWorker
{
    public void Work() { Console.WriteLine("Human working..."); }
    public void Eat() { Console.WriteLine("Human eating..."); }
    public void Sleep() { Console.WriteLine("Human sleeping..."); }
}

public class RobotWorker : IWorker
{
    public void Work() { Console.WriteLine("Robot working..."); }
    public void Eat() { /* Robots don't eat */ }
    public void Sleep() { /* Robots don't sleep */ }
}
```

Here, `RobotWorker` is forced to implement `Eat()` and `Sleep()` which are not relevant to its functionality.

**C# Example (Adhering to ISP):**

```csharp
public interface IWorkable
{
    void Work();
}

public interface IEatable
{
    void Eat();
}

public interface ISleepable
{
    void Sleep();
}

public class HumanWorkerISP : IWorkable, IEatable, ISleepable
{
    public void Work() { Console.WriteLine("Human working..."); }
    public void Eat() { Console.WriteLine("Human eating..."); }
    public void Sleep() { Console.WriteLine("Human sleeping..."); }
}

public class RobotWorkerISP : IWorkable
{
    public void Work() { Console.WriteLine("Robot working..."); }
}
```

Now, `RobotWorkerISP` only implements the `IWorkable` interface, adhering to the principle and avoiding unnecessary implementations.

### 5. Dependency Inversion Principle (DIP)

**Definition:**

1.  High-level modules should not depend on low-level modules. Both should depend on abstractions.
2.  Abstractions should not depend on details. Details should depend on abstractions.

**Explanation:** This principle is about decoupling modules. Instead of high-level modules (which contain important business logic) depending directly on low-level modules (implementation details like database access or UI), both should depend on abstractions (interfaces). This makes the system more flexible, testable, and maintainable.

**C# Example (Violation):**

```csharp
public class LightBulb
{
    public void TurnOn() { Console.WriteLine("LightBulb: On"); }
    public void TurnOff() { Console.WriteLine("LightBulb: Off"); }
}

public class ElectricPowerSwitch
{
    private LightBulb _bulb;

    public ElectricPowerSwitch()
    {
        _bulb = new LightBulb(); // Direct dependency on a low-level module
    }

    public void Press()
    {
        if (/* switch is on */ true)
        {
            _bulb.TurnOff();
        }
        else
        {
            _bulb.TurnOn();
        }
    }
}
```

The `ElectricPowerSwitch` (high-level module) directly depends on `LightBulb` (low-level module). If you want to use a different device (e.g., a fan), you have to modify `ElectricPowerSwitch`, violating OCP as well.

**C# Example (Adhering to DIP):**

```csharp
public interface ISwitchableDevice
{
    void TurnOn();
    void TurnOff();
}

public class LightBulbDIP : ISwitchableDevice
{
    public void TurnOn() { Console.WriteLine("LightBulb: On"); }
    public void TurnOff() { Console.WriteLine("LightBulb: Off"); }
}

public class FanDIP : ISwitchableDevice
{
    public void TurnOff() { Console.WriteLine("Fan: Off"); }
    public void TurnOn() { Console.WriteLine("Fan: On"); }
}

public class ElectricPowerSwitchDIP
{
    private ISwitchableDevice _device;

    // Dependency injected through constructor
    public ElectricPowerSwitchDIP(ISwitchableDevice device)
    {
        _device = device;
    }

    public void Press()
    {
        if (/* switch is on */ true)
        {
            _device.TurnOff();
        }
        else
        {
            _device.TurnOn();
        }
    }
}
// Usage:
// ISwitchableDevice bulb = new LightBulbDIP();
// ElectricPowerSwitchDIP bulbSwitch = new ElectricPowerSwitchDIP(bulb);
// bulbSwitch.Press();

// ISwitchableDevice fan = new FanDIP();
// ElectricPowerSwitchDIP fanSwitch = new ElectricPowerSwitchDIP(fan);
// fanSwitch.Press();
```

Now, `ElectricPowerSwitchDIP` depends on the `ISwitchableDevice` abstraction, not on concrete implementations. This allows for easy substitution of devices without modifying the switch logic. This is also how Dependency Injection (DI) frameworks work.

## GRASP Principles

GRASP (General Responsibility Assignment Software Patterns) are a set of general principles used in object-oriented design to assign responsibilities to classes. These principles help in creating maintainable, flexible, and understandable systems.

### 1. Information Expert

**Definition:** Assign responsibility to the information expert—the class that has the information necessary to fulfill the responsibility.

**Explanation:** This is a fundamental principle. If a class needs to perform an action or calculate a value, it should be the class that holds the data required for that action or calculation. This promotes encapsulation and ensures that responsibilities are close to the data they operate on.

**C# Example:**

Instead of a `Calculator` class calculating the total for an `Order`, the `Order` class itself should calculate its total because it holds the information (list of items, quantities, prices).

```csharp
public class LineItem
{
    public string ProductName { get; set; }
    public int Quantity { get; set; }
    public decimal Price { get; set; }

    public decimal GetTotalPrice()
    {
        return Quantity * Price;
    }
}

public class OrderGRASP
{
    public List<LineItem> LineItems { get; private set; } = new List<LineItem>();

    public void AddLineItem(LineItem item)
    {
        LineItems.Add(item);
    }

    // Information Expert: Order calculates its own total
    public decimal CalculateTotal()
    {
        decimal total = 0;
        foreach (var item in LineItems)
        {
            total += item.GetTotalPrice();
        }
        return total;
    }
}
```

### 2. Creator

**Definition:** Assign class `B` the responsibility to create an instance of class `A` if `B` aggregates `A` objects, `B` contains `A` objects, `B` records `A` objects, or `B` closely uses `A` objects.

**Explanation:** The Creator pattern suggests that the class that has the data or is responsible for closely using an object should be responsible for creating it. This helps keep creation logic localized and often aligns with High Cohesion.

**C# Example:**

An `Order` usually creates `LineItem` instances for itself.

```csharp
public class OrderCreator
{
    public List<LineItem> LineItems { get; private set; } = new List<LineItem>();

    public LineItem AddItem(string productName, int quantity, decimal price)
    {
        LineItem newItem = new LineItem
        {
            ProductName = productName,
            Quantity = quantity,
            Price = price
        };
        LineItems.Add(newItem);
        return newItem;
    }
}
```

### 3. Low Coupling

**Definition:** Assign responsibilities such that coupling remains low. Coupling is a measure of how strongly one element is connected to, knows about, or depends on other elements.

**Explanation:** Low coupling means that changes in one part of the system have minimal impact on other parts. This makes the system easier to understand, maintain, test, and reuse. Achieving low coupling often involves using interfaces and dependency injection (as seen in DIP).

**C# Example (revisiting DIP):**

The DIP example for `ElectricPowerSwitchDIP` and `ISwitchableDevice` also demonstrates low coupling. The switch is not coupled to a specific type of device, only to the abstraction `ISwitchableDevice`.

### 4. High Cohesion

**Definition:** Assign responsibilities such that cohesion remains high. Cohesion is a measure of how strongly related and focused the responsibilities of a single element are.

**Explanation:** A highly cohesive module or class performs a single, well-defined function. High cohesion typically goes hand-in-hand with low coupling. SRP is a specific way to achieve high cohesion. High cohesion makes classes easier to understand, test, and maintain.

**C# Example (revisiting SRP):**

The refactored `Order`, `OrderRepository`, and `OrderPrinter` classes from the SRP example demonstrate high cohesion, as each class has a very focused set of responsibilities.

### 5. Controller

**Definition:** Assign the responsibility for handling system events (like user interface actions or messages from other systems) to a class that represents the overall system, or a use case handler.

**Explanation:** The Controller pattern suggests a central point for handling incoming requests or events. For UI, this might be a UI controller (like in MVC). For a system, it might be a facade or a specific use case handler. The controller orchestrates the work, delegating tasks to other objects, but doesn't perform all the work itself.

**C# Example (ASP.NET Core Controller):**

```csharp
public class ProductControllerGRASP : ControllerBase
{
    private readonly IProductService _productService; // Delegates to a service

    public ProductControllerGRASP(IProductService productService)
    {
        _productService = productService;
    }

    [HttpPost]
    public IActionResult CreateProduct([FromBody] CreateProductRequest request)
    {
        // Controller handles the incoming request and orchestrates the creation
        var product = _productService.CreateProduct(request.Name, request.Price);
        return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
    }

    [HttpGet("{id}")]
    public IActionResult GetProduct(int id) {
        // ... logic to retrieve product ...
        return Ok();
    }
}

public interface IProductService
{
    Product CreateProduct(string name, decimal price);
}
```

Here, `ProductControllerGRASP` acts as a controller, handling HTTP requests and delegating business logic to `IProductService`.

## Common Design Patterns

Design patterns are categorized into three main types: Creational, Structural, and Behavioral.

### Creational Patterns

These patterns provide various object creation mechanisms, which increase flexibility and reuse of existing code.

#### 1. Factory Method

**Definition:** Define an interface for creating an object, but let subclasses alter the type of objects that will be created.

**Explanation:** The Factory Method pattern allows you to defer instantiation of an object to its subclasses. It promotes loose coupling by removing the need to bind application-specific classes into your code. Instead of directly instantiating an object using `new`, you use a factory method that returns an instance of a product.

**C# Example:**

Consider a logging application that needs to log messages to different destinations (e.g., console, file, database).

```csharp
public interface ILogger
{
    void Log(string message);
}

public class ConsoleLogger : ILogger
{
    public void Log(string message)
    {
        Console.WriteLine($"Console Log: {message}");
    }
}

public class FileLogger : ILogger
{
    public void Log(string message)
    {
        Console.WriteLine($"File Log: {message}"); // Simulate writing to file
    }
}

public abstract class LoggerFactory
{
    public abstract ILogger CreateLogger();

    public void LogMessage(string message)
    {
        ILogger logger = CreateLogger();
        logger.Log(message);
    }
}

public class ConsoleLoggerFactory : LoggerFactory
{
    public override ILogger CreateLogger()
    {
        return new ConsoleLogger();
    }
}

public class FileLoggerFactory : LoggerFactory
{
    public override ILogger CreateLogger()
    {
        return new FileLogger();
    }
}

// Usage:
//LoggerFactory consoleFactory = new ConsoleLoggerFactory();
// consoleFactory.LogMessage("This is a console message.");

// LoggerFactory fileFactory = new FileLoggerFactory();
// fileFactory.LogMessage("This is a file message.");
```

#### 2. Singleton

**Definition:** Ensure a class has only one instance, and provide a global point of access to it.

**Explanation:** The Singleton pattern is used when you need to ensure that only one instance of a class exists throughout the application's lifecycle and that this single instance is easily accessible. Common use cases include logging, configuration management, and managing database connection pools.

**C# Example (Thread-safe Singleton):**

```csharp
public sealed class SingletonLogger
{
    private static readonly Lazy<SingletonLogger> lazy = new Lazy<SingletonLogger>(() => new SingletonLogger());

    public static SingletonLogger Instance
    {
        get { return lazy.Value; }
    }

    private SingletonLogger()
    {
        Console.WriteLine("SingletonLogger instance created.");
    }

    public void Log(string message)
    {
        Console.WriteLine($"Singleton Log: {message}");
    }
}

// Usage:
// SingletonLogger.Instance.Log("Application started.");
// SingletonLogger.Instance.Log("Another message.");
// (Only one "SingletonLogger instance created." message will appear)
```

### Structural Patterns

These patterns explain how to assemble objects and classes into larger structures while keeping these structures flexible and efficient.

#### 1. Adapter

**Definition:** Convert the interface of a class into another interface clients expect. Adapter lets classes work together that couldn't otherwise because of incompatible interfaces.

**Explanation:** The Adapter pattern acts as a connector between two incompatible interfaces. It allows objects with different interfaces to collaborate without changing their existing code. This is particularly useful when integrating legacy code or third-party libraries.

**C# Example:**

Suppose you have a legacy logging system `LegacyLogger` and a new application that expects an `ILogger` interface.

```csharp
// The target interface that the client expects
public interface IModernLogger
{
    void LogMessage(string message);
}

// The existing or third-party class with an incompatible interface
public class LegacyLogger
{
    public void WriteLog(string logText)
    {
        Console.WriteLine($"Legacy Log: {logText}");
    }
}

// The Adapter class that translates the ModernLogger interface to the LegacyLogger
public class LegacyLoggerAdapter : IModernLogger
{
    private readonly LegacyLogger _legacyLogger;

    public LegacyLoggerAdapter(LegacyLogger legacyLogger)
    {
        _legacyLogger = legacyLogger;
    }

    public void LogMessage(string message)
    {
        _legacyLogger.WriteLog(message); // Adapts the call
    }
}

// Usage:
// LegacyLogger legacy = new LegacyLogger();
// IModernLogger adapter = new LegacyLoggerAdapter(legacy);
// adapter.LogMessage("This message is logged via the adapter.");
```

#### 2. Decorator

**Definition:** Attach additional responsibilities to an object dynamically. Decorators provide a flexible alternative to subclassing for extending functionality.

**Explanation:** The Decorator pattern allows you to add new behaviors to individual objects without changing the functionality of other objects of the same class. It essentially "wraps" an object with a new object that adds extra behavior. This is an excellent alternative to inheritance for extending functionality, promoting OCP.

**C# Example:**

Imagine an `ICoffee` interface and different types of coffee. You want to add functionalities like `Milk` or `Sugar` dynamically.

```csharp
public interface ICoffee
{
    string GetDescription();
    double GetCost();
}

public class SimpleCoffee : ICoffee
{
    public string GetDescription() { return "Simple Coffee"; }
    public double GetCost() { return 5.0; }
}

// Base Decorator class (optional, but good practice)
public abstract class CoffeeDecorator : ICoffee
{
    protected ICoffee _coffee;

    public CoffeeDecorator(ICoffee coffee)
    {
        _coffee = coffee;
    }

    public virtual string GetDescription() { return _coffee.GetDescription(); }
    public virtual double GetCost() { return _coffee.GetCost(); }
}

public class MilkDecorator : CoffeeDecorator
{
    public MilkDecorator(ICoffee coffee) : base(coffee) { }

    public override string GetDescription() { return _coffee.GetDescription() + ", Milk"; }
    public override double GetCost() { return _coffee.GetCost() + 1.5; }
}

public class SugarDecorator : CoffeeDecorator
{
    public SugarDecorator(ICoffee coffee) : base(coffee) { }

    public override string GetDescription() { return _coffee.GetDescription() + ", Sugar"; }
    public override double GetCost() { return _coffee.GetCost() + 0.5; }
}

// Usage:
// ICoffee coffee = new SimpleCoffee();
// Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}"); // Simple Coffee - $5

// coffee = new MilkDecorator(coffee);
// Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}"); // Simple Coffee, Milk - $6.5

// coffee = new SugarDecorator(coffee);
// Console.WriteLine($"{coffee.GetDescription()} - ${coffee.GetCost()}"); // Simple Coffee, Milk, Sugar - $7
```

### Behavioral Patterns

These patterns are concerned with algorithms and the assignment of responsibilities between objects.

#### 1. Strategy

**Definition:** Define a family of algorithms, encapsulate each one, and make them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

**Explanation:** The Strategy pattern allows you to choose an algorithm at runtime. Instead of implementing a single algorithm directly, you create a family of algorithms, put each of them into a separate class, and make their objects interchangeable. This is a powerful way to adhere to OCP.

**C# Example:**

Consider different ways to sort a list of numbers.

```csharp
public interface ISortStrategy
{
    List<int> Sort(List<int> list);
}

public class BubbleSortStrategy : ISortStrategy
{
    public List<int> Sort(List<int> list)
    {
        Console.WriteLine("Sorting using Bubble Sort");
        // ... actual bubble sort implementation
        return list.OrderBy(x => x).ToList(); // Simplified for example
    }
}

public class QuickSortStrategy : ISortStrategy
{
    public List<int> Sort(List<int> list)
    {
        Console.WriteLine("Sorting using Quick Sort");
        // ... actual quick sort implementation
        return list.OrderBy(x => x).ToList(); // Simplified for example
    }
}

public class Sorter
{
    private ISortStrategy _strategy;

    public Sorter(ISortStrategy strategy)
    {
        _strategy = strategy;
    }

    public void SetStrategy(ISortStrategy strategy)
    {
        _strategy = strategy;
    }

    public List<int> SortList(List<int> list)
    {
        return _strategy.Sort(list);
    }
}

// Usage:
// List<int> numbers = new List<int> { 3, 1, 4, 1, 5, 9, 2, 6 };

// Sorter sorter = new Sorter(new BubbleSortStrategy());
// sorter.SortList(numbers);

// sorter.SetStrategy(new QuickSortStrategy());
// sorter.SortList(numbers);
```

#### 2. Observer

**Definition:** Define a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

**Explanation:** The Observer pattern is useful when you have an object (the subject) that needs to notify a group of other objects (the observers) when its state changes, without making assumptions about who those observers are. This promotes loose coupling between the subject and its observers.

**C# Example (built-in events or custom implementation):**

Using C# events is a common way to implement the Observer pattern.

```csharp
// Subject
public class Stock : IDisposable
{
    private string _symbol;
    private decimal _price;

    public event EventHandler<decimal> PriceChanged;

    public Stock(string symbol, decimal price)
    {
        _symbol = symbol;
        _price = price;
    }

    public decimal Price
    {
        get { return _price; }
        set
        {
            if (_price != value)
            {
                _price = value;
                OnPriceChanged(value);
            }
        }
    }

    protected virtual void OnPriceChanged(decimal newPrice)
    {
        PriceChanged?.Invoke(this, newPrice);
    }

    public void Dispose()
    {
        PriceChanged = null;
    }
}

// Observer
public class StockMonitor
{
    private string _name;

    public StockMonitor(string name)
    {
        _name = name;
    }

    public void Subscribe(Stock stock)
    {
        stock.PriceChanged += HandlePriceChange;
        Console.WriteLine($"{_name} subscribed to {stock.GetType().Name} price changes.");
    }

    public void Unsubscribe(Stock stock)
    {
        stock.PriceChanged -= HandlePriceChange;
        Console.WriteLine($"{_name} unsubscribed from {stock.GetType().Name} price changes.");
    }

    private void HandlePriceChange(object sender, decimal newPrice)
    {
        Stock stock = sender as Stock;
        Console.WriteLine($"{_name}: Stock {stock.GetType().Name} price changed to {newPrice:C}");
    }
}

// Usage:
// Stock googleStock = new Stock("GOOG", 1500m);
// StockMonitor monitor1 = new StockMonitor("Monitor 1");
// StockMonitor monitor2 = new StockMonitor("Monitor 2");

// monitor1.Subscribe(googleStock);
// monitor2.Subscribe(googleStock);

// googleStock.Price = 1505m; // Both monitors are notified
// googleStock.Price = 1510m;

// For proper cleanup, unsubscribe when observers are no longer needed.
// monitor1.Unsubscribe(googleStock);
// googleStock.Price = 1515m; 
// (Only Monitor 2 will be notified now)
```

## Conclusion

Design principles (like SOLID and GRASP) and design patterns are indispensable tools for any software architect. They provide a framework for thinking about software design, leading to systems that are more adaptable, resilient, and easier to evolve.

Mastering these concepts allows you to:

-   **Build more maintainable code:** Reducing complexity and making changes easier.
-   **Improve code quality:** Leading to fewer bugs and more reliable systems.
-   **Increase team productivity:** By providing a common language and proven solutions.
-   **Make informed design decisions:** Understanding the trade-offs involved in different approaches.

By consistently applying these principles and patterns, you can elevate the quality of your software designs from good to excellent.

This concludes our deep dive into Design Patterns and Principles. Our next topic will be `Introduction to Cloud Native Architectures`.