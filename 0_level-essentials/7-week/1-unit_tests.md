# **Юнит-тесты (xUnit/NUnit + Moq)**

**Цель**: Проверять отдельные компоненты приложения (классы, методы) изолированно.

## **Зачем тестировать?**

Представьте: вы написали метод для расчета скидки. Юнит-тест — это робот, который проверит его работу на сотне примеров за секунду, чтобы вы не делали это вручную.

---

## **Пример теста для калькулятора**

**Класс для тестирования**:

```csharp
public class Calculator
{
    public int Add(int a, int b) => a + b;
}
```

**Тест с использованием xUnit**:

```csharp
public class CalculatorTests
{
    [Fact] // Атрибут для метода-теста
    public void Add_TwoNumbers_ReturnsSum()
    {
        // Arrange (подготовка)
        var calculator = new Calculator();

        // Act (действие)
        int result = calculator.Add(2, 3);

        // Assert (проверка)
        Assert.Equal(5, result);
    }
}
```

## **Мокирование зависимостей (Moq)**

Предположим, ваш класс зависит от сервиса базы данных. Чтобы не подключать реальную БД в тестах, используйте «мок» (заглушку).

**Пример**:

```csharp
public interface IDatabaseService
{
    bool IsUserAdmin(int userId);
}

public class UserService
{
    private readonly IDatabaseService _db;
    public UserService(IDatabaseService db) => _db = db;

    public string GetUserRole(int userId)
        => _db.IsUserAdmin(userId) ? "Admin" : "User";
}
```

**Тест с Moq**:

```csharp
[Fact]
public void GetUserRole_AdminUser_ReturnsAdmin()
{
    // Arrange
    var mockDb = new Mock<IDatabaseService>();
    mockDb.Setup(db => db.IsUserAdmin(1)).Returns(true); // Заглушка

    var userService = new UserService(mockDb.Object);

    // Act
    string role = userService.GetUserRole(1);

    // Assert
    Assert.Equal("Admin", role);
}
```
