# **Архитектура веб-приложений: MVC и REST API**

**Цель**: Понять разницу между MVC и API, научиться создавать endpoints.

## **Зачем нужна архитектура?**

Представь, что ты строишь дом:

- **Без архитектуры** — куча кирпичей, труб и проводов в случайном порядке.
- **С архитектурой** — четкий план: где кухня, как подведена вода, где розетки.

В веб-разработке архитектура:

- **Упрощает поддержку**
- **Позволяет работать команде**
- **Делает код предсказуемым**

## **MVC (Model-View-Controller)**

**Аналогия с рестораном:**

- **Модель (Model)** = Повар и склад продуктов

  - Работает с данными (готовит блюда из ингредиентов).
  - _Пример в коде:_ класс `Product` в C#, который хранит данные о товаре.

  ```csharp
  public class Product
  {
      public int Id { get; set; }
      public string Name { get; set; }
      public decimal Price { get; set; }
  }
  ```

- **Контроллер (Controller)** = Официант

  - Принимает заказ от клиента → передает повару → несет готовое блюдо.
  - _Пример:_ контроллер в ASP.NET Core.

  ```csharp
  public class ProductController : Controller
  {
      public IActionResult Details(int id)
      {
          // Получаем данные из модели
          var product = _productService.GetProduct(id);
          // Передаем во View
          return View(product);
      }
  }
  ```

- **Представление (View)** = Подача блюда (тарелка, украшения)

  - Показывает данные пользователю.
  - _Пример:_ HTML-страница с данными о товаре.

  ```html
  <!-- Views/Product/Details.cshtml -->
  <h1>@Model.Name</h1>
  <p>Цена: @Model.Price руб.</p>
  ```

## **REST API**

**Аналогия с почтовым сервисом:**

- Вы отправляете **конверт** (HTTP-запрос) с адресом (URL) и инструкцией (метод). Сервер возвращает **посылку** (JSON/XML).

**Основные принципы:**

1. **Ресурсы** — всё, с чем вы работаете (товары, пользователи).
   - URL: `https://api.shop.com/products`
2. **HTTP-методы** — действия с ресурсами:
   - `GET` — получить товар
   - `POST` — создать новый товар
   - `PUT` — обновить товар
   - `DELETE` — удалить товар

**Пример запросов:**

| Метод | URL             | Действие             |
| ----- | --------------- | -------------------- |
| GET   | /api/products   | Список всех товаров  |
| GET   | /api/products/5 | Товар с ID=5         |
| POST  | /api/products   | Добавить новый товар |

**Пример контроллера REST API в C#:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpGet]
    public IEnumerable<Product> GetProducts()
    {
        return _database.GetProducts();
    }

    [HttpGet("{id}")]
    public Product GetProduct(int id)
    {
        return _database.GetProductById(id);
    }

    [HttpPost]
    public IActionResult AddProduct(Product product)
    {
        _database.AddProduct(product);
        return CreatedAtAction(nameof(GetProduct), new { id = product.Id }, product);
    }
}
```

## **MVC vs REST API: когда что использовать?**

| Критерий           | MVC                          | REST API                    |
| ------------------ | ---------------------------- | --------------------------- |
| **Тип приложения** | Веб-сайты (HTML)             | Мобильные приложения, SPA   |
| **Данные**         | HTML + CSS                   | JSON/XML                    |
| **Пример**         | Интернет-магазин с каталогом | Приложение для доставки еды |

**Комбинированный пример:**

- **Frontend** (React/Vue) → **REST API** (ASP.NET Core) → **База данных**
- **Backend** (ASP.NET Core MVC) → Админка сайта

---

## **Практические советы для .NET**

1. **MVC в ASP.NET Core:**

   - Шаблон проекта: `dotnet new mvc`
   - Папки: `Models`, `Views`, `Controllers`.

2. **REST API в ASP.NET Core:**

   - Шаблон: `dotnet new webapi`
   - Атрибуты: `[ApiController]`, `[HttpGet]`, `[HttpPost]`.

3. **Советы:**
   - Всегда возвращайте правильные **HTTP-статусы** (200 OK, 404 Not Found).
   - Используйте **DTO** (Data Transfer Objects) для API.
   - Документируйте API через **Swagger**.

## **6. Проверь себя**

1. Представь, что делаешь приложение для библиотеки:

   - MVC: страница с деталями книги (HTML).
   - REST API: метод `GET /api/books/{id}` для мобильного приложения.

2. Какой код нужен для удаления товара через API?

   ```csharp
   [HttpDelete("{id}")]
   public IActionResult DeleteProduct(int id)
   {
       _database.DeleteProduct(id);
       return NoContent(); // 204 статус
   }
   ```

**Главное:**

- **MVC** — для веб-страниц.
- **REST API** — для обмена данными между приложениями.
- В реальных проектах они часто работают вместе!
