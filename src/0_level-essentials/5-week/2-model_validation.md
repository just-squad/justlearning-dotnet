# **Валидация моделей**

**Цель**: Гарантировать корректность данных, приходящих от клиента.

## **Встроенные атрибуты валидации**

- `[Required]`: Поле обязательно.
- `[StringLength(100)]`: Максимальная длина строки.
- `[Range(1, 120)]`: Число в диапазоне.
- `[EmailAddress]`: Проверка формата email.

**Пример модели с валидацией**:

```csharp
public class Order
{
    [Required]
    public int Id { get; set; }

    [StringLength(200)]
    public string Description { get; set; }

    [Range(1, 1000)]
    public decimal Price { get; set; }
}
```

**Проверка валидации в контроллере**:

```csharp
[HttpPost]
public IActionResult CreateOrder([FromBody] Order order)
{
    if (!ModelState.IsValid) // Автоматически проверяется благодаря [ApiController]
    {
        return BadRequest(ModelState); // Возвращает ошибки валидации
    }
    // Сохранение заказа...
    return Ok();
}
```

## **Кастомные валидаторы**

Создайте класс, унаследованный от `ValidationAttribute`:

```csharp
public class NoProfanityAttribute : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, ValidationContext context)
    {
        string text = value as string;
        if (text != null && text.Contains("мат"))
        {
            return new ValidationResult("Некорректный язык!");
        }
        return ValidationResult.Success;
    }
}

// Использование
public class Post
{
    [NoProfanity]
    public string Content { get; set; }
}
```
