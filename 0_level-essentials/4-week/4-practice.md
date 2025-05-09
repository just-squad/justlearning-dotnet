# **Практика на неделю**

1. **Интеграция EF Core в API из 3-й недели**:

   - Замените хранение пользователей в памяти на PostgreSQL.
   - Реализуйте CRUD для пользователей через EF Core.
   - Добавьте сущность `Post` (статья) с полями `Title`, `Content` и связью с `User`.

2. **Мини-проект: Система бронирования билетов**:
   - Сущности:
     - `Event` (мероприятие): Id, Name, Date, AvailableSeats.
     - `Ticket` (билет): Id, EventId, BuyerName.
   - API методы:
     - `POST /api/events` → Создать мероприятие.
     - `GET /api/events` → Список мероприятий.
     - `POST /api/tickets` → Купить билет (уменьшать AvailableSeats).
     - `DELETE /api/tickets/{id}` → Вернуть билет.

---

## **Пример кода для мини-проекта**

**Модель `Event`**:

```csharp
public class Event
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime Date { get; set; }
    public int AvailableSeats { get; set; }
    public List<Ticket> Tickets { get; set; } = new();
}
```

**Контроллер**:

```csharp
[HttpPost("tickets")]
public IActionResult BuyTicket([FromBody] TicketRequest request)
{
    var event = context.Events.Find(request.EventId);
    if (event == null || event.AvailableSeats < 1)
    {
        return BadRequest("Нет мест");
    }

    event.AvailableSeats--;
    var ticket = new Ticket { BuyerName = request.BuyerName, EventId = request.EventId };
    context.Tickets.Add(ticket);
    context.SaveChanges();

    return Ok(ticket);
}
```
