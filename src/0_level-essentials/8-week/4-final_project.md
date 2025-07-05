# **Финальный проект: веб-API (аналог Trello)**

**Требования**:

1. **Сущности**:

   - `Board` (доска): Название, описание.
   - `List` (колонка): Принадлежит доске, содержит задачи.
   - `Card` (задача): Название, описание, статус (TODO/In Progress/Done).

2. **Функционал**:

   - Аутентификация через JWT.
   - CRUD для досок, колонок и задач.
   - Фильтрация задач по статусу.
   - Логирование действий (создание, перемещение карточек).
   - Тесты: 5+ юнит-тестов, 2+ интеграционных.

3. **Инфраструктура**:
   - PostgreSQL + EF Core.
   - Docker-контейнеры для API и БД.
   - Пайплайн CI/CD (сборка, тесты, деплой).

---

## **Пример структуры проекта**

```bash
TrelloApi/
├── Controllers/
│   ├── BoardsController.cs
│   ├── ListsController.cs
│   └── AuthController.cs
├── Services/
│   ├── BoardService.cs
│   └── JwtService.cs
├── Migrations/
├── Dockerfile
├── docker-compose.yml
└── TrelloApi.csproj
```

---

## **Код для перемещения карточки**

```csharp
[HttpPut("cards/{id}/move")]
[Authorize]
public IActionResult MoveCard(int id, [FromBody] MoveCardRequest request)
{
    var card = _dbContext.Cards.Find(id);
    if (card == null) return NotFound();

    card.ListId = request.NewListId;
    _dbContext.SaveChanges();

    _logger.LogInformation($"Карточка {id} перемещена в список {request.NewListId}");
    return Ok(card);
}
```

---

## **Тесты**

```csharp
[Fact]
public void MoveCard_ValidData_UpdatesListId()
{
    // Arrange
    var card = new Card { Id = 1, ListId = 1 };
    var mockDb = new Mock<AppDbContext>();
    mockDb.Setup(db => db.Cards.Find(1)).Returns(card);

    var service = new CardService(mockDb.Object);

    // Act
    service.MoveCard(1, 2);

    // Assert
    Assert.Equal(2, card.ListId);
}
```

---

### **Презентация проекта и код-ревью**

1. **Что подготовить**:

   - Демонстрация работы API через Postman/Swagger.
   - Показать дашборд в Grafana с метриками.
   - Примеры логов в Kibana.

2. **Код-ревью**:
   - Проверка SOLID, чистоты кода.
   - Качество тестов (покрытие, изоляция).
   - Безопасность (хеширование паролей, валидация).

---

### **Советы для финального проекта**

1. **Используйте паттерн Repository** для абстракции работы с БД.
2. **Документируйте API** с помощью Swagger.
3. **Настройте Health Checks** для мониторинга работоспособности:

   ```csharp
   app.MapHealthChecks("/health");
   ```

---

### **Частые ошибки**

1. **Игнорирование миграций**: Не забывайте применять их в Docker-контейнере.
2. **Слабые секреты**: Храните JWT-ключ и пароли БД в переменных окружения.
3. **Отсутствие обработки ошибок**: Возвращайте 500 только для критических сбоев.
