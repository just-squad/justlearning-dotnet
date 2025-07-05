# **Интеграционные тесты**

**Цель**: Проверять взаимодействие компонентов (например, API и БД).

## **Тестирование API**

ASP.NET Core позволяет запускать тестовый сервер и отправлять HTTP-запросы.

**Пример теста для контроллера**:

```csharp
public class UsersControllerTests : IClassFixture<WebApplicationFactory<Program>>
{
    private readonly WebApplicationFactory<Program> _factory;

    public UsersControllerTests(WebApplicationFactory<Program> factory)
    {
        _factory = factory;
    }

    [Fact]
    public async Task GetUsers_ReturnsSuccessStatusCode()
    {
        // Arrange
        var client = _factory.CreateClient();

        // Act
        var response = await client.GetAsync("/api/users");

        // Assert
        response.EnsureSuccessStatusCode(); // Проверяет, что статус 200-299
    }
}
```

**Настройка тестовой БД**:
Используйте SQLite в памяти или Docker-контейнер с тестовой БД, чтобы не затрагивать продакшен.
