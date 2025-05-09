# **Работа с HTTP: запросы, ответы, статус-коды**

**Цель**: Правильно формировать HTTP-ответы и понимать статус-коды.

## **Статус-коды**

- `200 OK`: Успешный запрос.
- `201 Created`: Ресурс создан (обычно после `POST`).
- `400 Bad Request`: Неверные данные от клиента.
- `404 Not Found`: Ресурс не найден.
- `500 Internal Server Error`: Ошибка на сервере.

**Пример возврата статусов**:

```csharp
[HttpPost]
public IActionResult CreatePost([FromBody] Post post)
{
    if (post == null)
    {
        return BadRequest(); // 400
    }
    // Сохранение поста...
    return CreatedAtAction(nameof(GetPostById), new { id = post.Id }, post); // 201
}
```

---

## **Параметры запроса**

Данные можно получать из:

- Тела запроса (`[FromBody]`).
- URL (`[FromRoute]`, `[FromQuery]`).
- Заголовков (`[FromHeader]`).

**Пример**:

```csharp
// GET api/posts?search=текст
[HttpGet]
public IActionResult SearchPosts([FromQuery] string search)
{
    // Поиск постов по ключевому слову
    return Ok(posts);
}
```

---
