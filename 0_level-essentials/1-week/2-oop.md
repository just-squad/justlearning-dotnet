# ООП: Классы, объекты, наследование

**Цель**: Понять, как моделировать реальные сущности с помощью классов.

## Класс и объект

- **Класс** — это чертеж. Например, чертеж "Автомобиль" с полями `Model` и `Speed`.
- **Объект** — конкретный экземпляр по этому чертежу. Например, автомобиль "Tesla" со скоростью 250 км/ч.

**Пример**:

```csharp
// Чертеж класса "Автомобиль"
class Car
{
    public string Model; // Поле: модель
    public int Speed;    // Поле: скорость

    // Метод: увеличить скорость
    public void Accelerate(int boost)
    {
        Speed += boost;
    }
}

// Создание объекта
Car myCar = new Car();
myCar.Model = "Tesla";
myCar.Accelerate(50);
Console.WriteLine(myCar.Speed); // 50
```

## Наследование

Класс-потомок наследует поля и методы родителя.  
Пример: Класс `ElectricCar` наследует все от `Car`, но добавляет свойство `BatteryLevel`.

```csharp
class ElectricCar : Car
{
    public int BatteryLevel { get; set; }
}

ElectricCar tesla = new ElectricCar();
tesla.Model = "Model S"; // Поле унаследовано от Car
tesla.BatteryLevel = 80; // Собственное поле
```
