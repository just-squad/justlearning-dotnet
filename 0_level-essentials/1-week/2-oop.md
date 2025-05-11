# **ООП: Классы, объекты, наследование**

**Цель**: Понять, как моделировать реальные сущности с помощью классов.

## ООП

Представь, что ты строишь дом. Вместо того чтобы создавать его из сырых материалов, ты будешь использовать уже готовые компоненты: двери, окна, стены. ООП работает подобным образом. Это подход к программированию, где программное обеспечение создается из взаимодействующих объектов, каждый из которых представляет собой отдельный "компонент" с определенными свойствами и функциями.

ООП основано на четырех основных концепциях:

1. **Инкапсуляция** - сокрытие внутренней реализации и предоставление безопасного интерфейса
1. **Наследование** - возможность создавать новые классы на основе существующих
1. **Полиморфизм** - возможность объектов вести себя по-разному в зависимости от контекста
1. **Абстракция** - работа с объектами на высоком уровне, без погружения в детали реализации

## Класс и объект

- **Класс** — это "чертеж" или "шаблон" для создания объектов. Он определяет, какие данные (поля) и поведение (методы) будут у объектов этого класса. Например, чертеж "Автомобиль" с полями `Model` и `Speed`.
- **Объект** — конкретный экземпляр по этому чертежу. Например, автомобиль "Tesla" со скоростью 250 км/ч. У каждого объекта есть:
  - Состояние (значения полей)
  - Поведение (методы)

**Пример**:

```csharp
// Чертеж класса "Автомобиль"
class Car
{
    // Поля (характеристики автомобиля)
    public string Brand;
    public string Model;
    public int Year;
    public string Color;

    // Метод (поведение автомобиля)
    public void Drive()
    {
        Console.WriteLine($"{Brand} {Model} едет вперед!");
    }

    public void Brake()
    {
        Console.WriteLine($"{Brand} {Model} остановился.");
    }
}

// Создание объектов (экземпляров класса)
Car myCar = new Car();
myCar.Brand = "Ford";
myCar.Model = "Mustang";
myCar.Year = 2020;
myCar.Color = "Red";

Car yourCar = new Car();
yourCar.Brand = "Toyota";
yourCar.Model = "Camry";
yourCar.Year = 2022;
yourCar.Color = "Blue";

// Использование методов
myCar.Drive();  // Ford Mustang едет вперед!
yourCar.Brake(); // Toyota Camry остановился.
```

## Конструктор

Конструктор - это специальный метод, который вызывается при создании объекта. Он помогает инициализировать объект.

```csharp
public class Car
{
    public string Brand;
    public string Model;
    public int Year;
    public string Color;

    // Конструктор
    public Car(string brand, string model, int year, string color)
    {
        Brand = brand;
        Model = model;
        Year = year;
        Color = color;
    }

    public void Drive()
    {
        Console.WriteLine($"{Brand} {Model} едет вперед!");
    }
}

// Создание объекта с использованием конструктора
Car myCar = new Car("Ford", "Mustang", 2020, "Red");
myCar.Drive();
```

По умолчанию у каждого класса существует пустой конструктор. Его даже можно не писать самостоятельно. Он автоматически создается при сборке программы

```csharp
public class Car
{
    public string Brand;
    public string Model;
    public int Year;
    public string Color;

    // Конструктор по умолчанию. Он существует даже если его не определять самостоятельно
    public Car()
    {
    }

    ...
}

// Вызов конструктора по умолчанию
Car myCar = new Car();
```

Если же мы переопределяем конструктор (создаем самостоятельно, например конструктор с параметрами), то конструктор по умолчанию становится не доступным, а остается только тот конструктор, который был самостоятельно определен.

```csharp
public class Car
{
    public string Brand;
    public string Model;
    public int Year;
    public string Color;

    // Конструктор
    public Car(string brand, string model, int year, string color)
    {
        Brand = brand;
        Model = model;
        Year = year;
        Color = color;
    }

    ...
}

// ОШИБКА! мы не можем вызывать конструктор по умолчанию, если самостоятельно определили свой конструктор.
Car myCar = new Car();
```

Конструкторов у класса может быть сколь угодное количество. Можно так же восстановить функционал конструктора по умолчанию, просто определив его руками самостоятельно.

Если мы создали несколько конструкторов, и хотим упростить внутреннюю инициализацию полей, можно воспользоваться ключевым словом `this`, которое помогает передать параметры в конструктор с самым большим количеством параметров. Например:

```csharp
public class Car
{
    public string Brand;
    public string Model;
    public int Year;
    public string Color;

    // Конструктор
    public Car(string brand, string model, int year, string color)
    {
        Brand = brand;
        Model = model;
        Year = year;
        Color = color;
    }

    // Этот конструктор под капотом вызовет тот, который был определен выше. Потому что используется ключевое слово this.
    // Но вместо параметров года производства и цвета будут передаваться константные значения 1991 и red соответственно.
    public Car(string brand, string model) : this(brand, model, 1991, "red")
    {
        Brand = brand;
        Model = model;
    }

    ...
}
```

Используя модификаторы доступа (поговорим подробнее позже) мы можем, например, скрывать конструкторы с самым большим количеством параметров, давая возможность пользователю использовать только один или другой набор параметров. Например:

```csharp
public class Car
{
    public string Brand;
    public string Model;
    public int? Year;
    public string? Color;

    // базовый приватный конструктор, который используется всеми остальными конструкторами.
    private Car(string brand, string model, int? year, string? color)
    {
        Brand = brand;
        Model = model;
        Year = year;
        Color = color;
    }

    public Car(string brand, string model) : this(brand, model, null, null)
    {
        Brand = brand;
        Model = model;
    }

    public Car(string brand, string model, int year) : this(brand, model, year, null)
    {
        Brand = brand;
        Model = model;
    }

    public Car(string brand, string model, int year, string color) : this(brand, model, year, color)
    {
        Brand = brand;
        Model = model;
    }

    ...
}
```

## Инкапсуляция

Инкапсуляция - это принцип, согласно которому детали реализации объекта скрыты от внешнего мира, и доступ к данным объекта осуществляется только через его методы.

**Пример из жизни**
Ты управляешь автомобилем с помощью руля, педалей и селектора КПП, но тебе абсолютно не важно знать, как именно работает двигатель внутри.

```csharp
public class BankAccount
{
    // Поле с приватным доступом (нельзя изменить напрямую извне)
    private decimal balance;

    // Публичный метод для изменения баланса
    public void Deposit(decimal amount)
    {
        if (amount > 0)
        {
            balance += amount;
            Console.WriteLine($"На счет добавлено: {amount}");
        }
    }

    public void Withdraw(decimal amount)
    {
        if (amount > 0 && amount <= balance)
        {
            balance -= amount;
            Console.WriteLine($"Со счета снято: {amount}");
        }
        else
        {
            Console.WriteLine("Недостаточно средств или неверная сумма");
        }
    }

    // Публичный метод для получения баланса
    public decimal GetBalance()
    {
        return balance;
    }
}

// Использование
BankAccount account = new BankAccount();
account.Deposit(1000);
account.Withdraw(500);
Console.WriteLine($"Текущий баланс: {account.GetBalance()}");
```

## Свойства (Properties)

В C# для работы с полями класса часто используют свойства - это "умные" поля с возможностью добавить логику при чтении или записи.

```csharp
public class Person
{
    private string name;
    private int age;

    // Свойство Name с проверкой
    public string Name
    {
        get { return name; }
        set
        {
            if (!string.IsNullOrEmpty(value))
                name = value;
            else
                Console.WriteLine("Имя не может быть пустым");
        }
    }

    // Свойство Age с проверкой
    public int Age
    {
        get { return age; }
        set
        {
            if (value >= 0 && value <= 120)
                age = value;
            else
                Console.WriteLine("Возраст должен быть от 0 до 120");
        }
    }

    // Автоматическое свойство (компилятор создаст скрытое поле)
    public string Address { get; set; }
}

// Использование
Person person = new Person();
person.Name = "Иван";  // Корректное значение
person.Age = 150;      // Выведет сообщение об ошибке
person.Address = "Москва";

Console.WriteLine($"{person.Name}, {person.Age} лет, адрес: {person.Address}");
```

## Наследование

Наследование позволяет создавать новый класс на основе существующего, перенимая его характеристики и поведение.

**Пример из жизни**
Представь класс `Animal` (Животное) с общими свойствами для всех животных. От него можно унаследовать классы `Dog`, `Cat`, `Bird` и т.д., которые будут иметь специфические особенности.

```csharp
// Базовый класс
public class Animal
{
    public string Name { get; set; }
    public int Age { get; set; }

    public void Eat()
    {
        Console.WriteLine($"{Name} ест.");
    }

    public void Sleep()
    {
        Console.WriteLine($"{Name} спит.");
    }
}

// Производный класс
public class Dog : Animal
{
    public void Bark()
    {
        Console.WriteLine($"{Name} лает: Гав-гав!");
    }
}

// Производный класс
public class Cat : Animal
{
    public void Meow()
    {
        Console.WriteLine($"{Name} мяукает: Мяу-мяу!");
    }
}

// Использование
Dog myDog = new Dog();
myDog.Name = "Барсик";
myDog.Age = 3;
myDog.Eat();  // Унаследованный метод
myDog.Bark(); // Собственный метод

Cat myCat = new Cat();
myCat.Name = "Мурка";
myCat.Age = 2;
myCat.Sleep(); // Унаследованный метод
myCat.Meow();  // Собственный метод
```

## Полиморфизм

Полиморфизм позволяет объектам разных классов обрабатываться как объекты общего родительского класса, но вести себя по-разному.

```csharp
public class Shape
{
    public virtual void Draw()
    {
        Console.WriteLine("Рисую фигуру");
    }
}

public class Circle : Shape
{
    public override void Draw()
    {
        Console.WriteLine("Рисую круг");
    }
}

public class Square : Shape
{
    public override void Draw()
    {
        Console.WriteLine("Рисую квадрат");
    }
}

// Использование полиморфизма
List<Shape> shapes = new List<Shape>();
shapes.Add(new Shape());
shapes.Add(new Circle());
shapes.Add(new Square());

foreach (Shape shape in shapes)
{
    shape.Draw(); // Вызовется разная реализация для каждого типа
}
```

## Абстракция

Абстракция позволяет работать с объектами, не вдаваясь в детали их реализации.

**Пример из жизни**
Когда ты нажимаешь кнопку "Пуск" на микроволновке, абсолютно не важно знать, как именно она работает внутри. Ты просто используешь некий предоставленный интерфейс.

```csharp
// Абстрактный класс
public abstract class Vehicle
{
    public string Model { get; set; }

    // Абстрактный метод (без реализации)
    public abstract void Move();

    // Обычный метод
    public void Stop()
    {
        Console.WriteLine($"{Model} остановился");
    }
}

// Конкретный класс
public class Airplane : Vehicle
{
    public override void Move()
    {
        Console.WriteLine($"{Model} летит по небу");
    }
}

// Конкретный класс
public class Boat : Vehicle
{
    public override void Move()
    {
        Console.WriteLine($"{Model} плывет по воде");
    }
}

// Использование
Vehicle myVehicle = new Airplane();
myVehicle.Model = "Боинг 747";
myVehicle.Move(); // Боинг 747 летит по небу
myVehicle.Stop();

myVehicle = new Boat();
myVehicle.Model = "Яхта";
myVehicle.Move(); // Яхта плывет по воде
myVehicle.Stop();
```

## Интерфейсы

Интерфейс - это контракт, который определяет, какие методы должен реализовать класс. Класс может реализовывать несколько интерфейсов.

```csharp
// Определение интерфейса
public interface IDriveable
{
    void StartEngine();
    void StopEngine();
    void Accelerate();
}

public interface IRepairable
{
    void Repair();
}

// Класс, реализующий интерфейсы
public class Car : IDriveable, IRepairable
{
    public string Model { get; set; }

    public void StartEngine()
    {
        Console.WriteLine($"{Model}: Двигатель запущен");
    }

    public void StopEngine()
    {
        Console.WriteLine($"{Model}: Двигатель остановлен");
    }

    public void Accelerate()
    {
        Console.WriteLine($"{Model}: Ускоряется");
    }

    public void Repair()
    {
        Console.WriteLine($"{Model}: Ремонтируется");
    }
}

// Использование
IDriveable driveable = new Car();
driveable.Model = "Toyota";
driveable.StartEngine();
driveable.Accelerate();
driveable.StopEngine();

IRepairable repairable = (IRepairable)driveable;
repairable.Repair();
```

## Практический пример: Система управления библиотекой

Создадим более сложный пример, объединяющий все концепции ООП.

```csharp
using System;
using System.Collections.Generic;

namespace LibrarySystem
{
    // Абстрактный класс для всех элементов библиотеки
    public abstract class LibraryItem
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public int Year { get; set; }
        public bool IsAvailable { get; protected set; } = true;

        public abstract void DisplayInfo();

        public virtual void CheckOut()
        {
            if (IsAvailable)
            {
                IsAvailable = false;
                Console.WriteLine($"{Title} выдан");
            }
            else
            {
                Console.WriteLine($"{Title} уже выдан");
            }
        }

        public virtual void Return()
        {
            IsAvailable = true;
            Console.WriteLine($"{Title} возвращен");
        }
    }

    // Класс для книг
    public class Book : LibraryItem
    {
        public string Author { get; set; }
        public int PageCount { get; set; }

        public override void DisplayInfo()
        {
            Console.WriteLine($"Книга: {Title} ({Year})");
            Console.WriteLine($"Автор: {Author}, {PageCount} стр.");
            Console.WriteLine($"Статус: {(IsAvailable ? "Доступна" : "Выдана")}");
        }
    }

    // Класс для DVD
    public class DVD : LibraryItem
    {
        public string Director { get; set; }
        public int DurationMinutes { get; set; }

        public override void DisplayInfo()
        {
            Console.WriteLine($"DVD: {Title} ({Year})");
            Console.WriteLine($"Режиссер: {Director}, {DurationMinutes} мин.");
            Console.WriteLine($"Статус: {(IsAvailable ? "Доступен" : "Выдан")}");
        }

        public override void CheckOut()
        {
            Console.WriteLine("Для DVD требуется проверка возраста!");
            base.CheckOut();
        }
    }

    // Класс для пользователей библиотеки
    public class LibraryUser
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public string Email { get; set; }
        private List<LibraryItem> borrowedItems = new List<LibraryItem>();

        public void BorrowItem(LibraryItem item)
        {
            if (borrowedItems.Count < 5) // Максимум 5 предметов
            {
                item.CheckOut();
                borrowedItems.Add(item);
            }
            else
            {
                Console.WriteLine("Превышен лимит выдачи (5 предметов)");
            }
        }

        public void ReturnItem(LibraryItem item)
        {
            if (borrowedItems.Contains(item))
            {
                item.Return();
                borrowedItems.Remove(item);
            }
            else
            {
                Console.WriteLine("Этот предмет не был взят данным пользователем");
            }
        }

        public void DisplayBorrowedItems()
        {
            Console.WriteLine($"Пользователь {Name} имеет следующие предметы:");
            foreach (var item in borrowedItems)
            {
                Console.WriteLine($"- {item.Title}");
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            // Создаем элементы библиотеки
            Book book1 = new Book()
            {
                Id = 1,
                Title = "Война и мир",
                Author = "Лев Толстой",
                Year = 1869,
                PageCount = 1225
            };

            DVD dvd1 = new DVD()
            {
                Id = 2,
                Title = "Крестный отец",
                Director = "Фрэнсис Форд Коппола",
                Year = 1972,
                DurationMinutes = 175
            };

            // Создаем пользователя
            LibraryUser user = new LibraryUser()
            {
                Id = 1,
                Name = "Иван Иванов",
                Email = "ivan@example.com"
            };

            // Используем полиморфизм - обрабатываем разные типы как LibraryItem
            LibraryItem[] items = { book1, dvd1 };

            foreach (var item in items)
            {
                item.DisplayInfo();
                Console.WriteLine();
            }

            // Пользователь берет предметы
            user.BorrowItem(book1);
            user.BorrowItem(dvd1);

            // Показываем взятые предметы
            user.DisplayBorrowedItems();

            // Возвращаем один предмет
            user.ReturnItem(book1);

            // Показываем обновленный список
            user.DisplayBorrowedItems();
        }
    }
}
```
