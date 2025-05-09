# **Практика на неделю**

1. **Написание тестов для API**

   - Возьмите API из предыдущих недель (например, блог).
   - Напишите юнит-тесты для сервисов (например, проверка расчета рейтинга поста).
   - Напишите интеграционные тесты для `GET /api/posts` и `POST /api/posts`.

2. **Мини-проект: Приложение с мониторингом и логами**:
   - Добавьте метрики Prometheus в свой API.
   - Настройте Grafana-дашборд с графиками:
     - Количество запросов к `/api/posts`.
     - Среднее время ответа.
   - Залейте логи в Elasticsearch и найдите все ошибки 500 через Kibana.

---

## **Пример интеграционного теста**

```csharp
[Fact]
public async Task CreatePost_ValidData_ReturnsCreated()
{
    // Arrange
    var client = _factory.CreateClient();
    var post = new { Title = "Test", Content = "Hello World" };

    // Act
    var response = await client.PostAsJsonAsync("/api/posts", post);

    // Assert
    response.EnsureSuccessStatusCode();
    var responsePost = await response.Content.ReadFromJsonAsync<Post>();
    Assert.NotNull(responsePost?.Id);
}
```
