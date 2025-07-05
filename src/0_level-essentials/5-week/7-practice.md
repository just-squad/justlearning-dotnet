# **Практика на неделю**

1. **Добавление кэширования в API**:

   - Возьмите API из предыдущих недель (например, блог).
   - Реализуйте кэширование GET-запросов (например, списка постов) на 5 минут.
   - Используйте `MemoryCache` или Redis.

2. **Мини-проект: API для интернет-магазина**:
   - Сущности: `Product` (товар), `Cart` (корзина), `CartItem` (элемент корзины).
   - Методы:
     - `GET /api/products` → Список товаров с фильтрацией по категории.
     - `POST /api/cart/items` → Добавить товар в корзину.
     - `GET /api/cart` → Получить корзину с товарами.
   - Добавьте валидацию (например, нельзя добавить товар с нулевым количеством).

---

## **Пример кода для мини-проекта**

**Модель `CartItem`**:

```csharp
public class CartItem
{
    public int Id { get; set; }
    public int ProductId { get; set; }
    public int Quantity { get; set; }

    [ForeignKey("ProductId")]
    public Product Product { get; set; }
}
```

**Контроллер корзины**:

```csharp
[HttpPost("items")]
public async Task<IActionResult> AddToCart([FromBody] CartItemRequest request)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var product = await _dbContext.Products.FindAsync(request.ProductId);
    if (product == null)
    {
        return NotFound("Товар не найден");
    }

    var cartItem = new CartItem
    {
        ProductId = request.ProductId,
        Quantity = request.Quantity
    };
    _dbContext.CartItems.Add(cartItem);
    await _dbContext.SaveChangesAsync();

    return Ok(cartItem);
}
```
