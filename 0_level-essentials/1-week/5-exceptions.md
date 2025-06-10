# Исключения в C#: Полное руководство по обработке ошибок и лучшим практикам

---

## **Основы исключений: Что это и зачем нужно?**

Исключения — это механизм обработки ошибок, который прерывает нормальный поток выполнения программы при возникновении нештатной ситуации.

**Пример из жизни**:  
Представь, что ты заказываешь пиццу. Если пиццерия закрыта — это исключение. Ты либо:

1. Находишь другую пиццерию (обработка исключения)
2. Готовишь сам (альтернативное решение)
3. Остаешься голодным (аварийное завершение)

```csharp
try
{
    Pizza pizza = OrderPizza(); // Может выбросить PizzaException
}
catch (PizzaClosedException ex)
{
    CookAtHome();
}
catch (Exception ex)
{
    Console.WriteLine($"Неизвестная ошибка: {ex.Message}");
}
```

---

## **Обработка исключений: try-catch-finally**

### Базовый синтаксис

```csharp
try
{
    // Код, который может вызвать исключение
}
catch (SpecificException ex)
{
    // Обработка конкретного типа исключения
}
catch (Exception ex)
{
    // Общая обработка (должен быть последним)
}
finally
{
    // Код, выполняемый всегда (очистка ресурсов)
}
```

**Пример с файлом**:

```csharp
FileStream file = null;
try
{
    file = File.Open("data.txt", FileMode.Open);
    // Работа с файлом
}
catch (FileNotFoundException)
{
    Console.WriteLine("Файл не найден!");
}
catch (IOException ex)
{
    Console.WriteLine($"Ошибка ввода-вывода: {ex.Message}");
}
finally
{
    file?.Close(); // Гарантированное закрытие файла
}
```

## **Создание пользовательских исключений**

Создавай собственные исключения для специфических ошибок приложения:

```csharp
public class InvalidOrderException : Exception
{
    public int OrderId { get; }

    public InvalidOrderException(int orderId, string message)
        : base(message)
    {
        OrderId = orderId;
    }
}

// Использование
throw new InvalidOrderException(123, "Неверный статус заказа");
```

**Best practices**:

- Наследуйся от `Exception` или его потомков
- Добавляй конструкторы с сообщением и внутренним исключением
- Используй суффикс "Exception" в имени класса

---

## **Распространение исключений**

Исключения поднимаются по стеку вызовов до первого подходящего `catch`:

```csharp
void MethodA()
{
    try
    {
        MethodB();
    }
    catch (Exception ex)
    {
        // Логирование и повторный throw
        LogError(ex);
        throw; // Сохраняет оригинальный стек вызовов
    }
}

void MethodB()
{
    throw new InvalidOperationException("Ошибка в B");
}
```

**Важно**:

- `throw ex;` — перезаписывает стек вызовов
- `throw;` — сохраняет оригинальный стек

---

## **Проблемы производительности и памяти**

### Основные проблемы

1. **Создание объекта исключения**:

   - Сбор информации о стеке — дорогая операция
   - Пример: 1000 исключений/сек → 1 MB памяти

2. **Обработка в глубоких стеках**:

   - Чем глубже стек вызовов, тем дольше обработка

3. **Сборка мусора**:
   - Частые исключения → частые GC

**Тест производительности**:

```csharp
// Плохо: 10,000 исключений ≈ 50 ms
for (int i = 0; i < 10_000; i++)
{
    try { throw new Exception(); }
    catch { }
}

// Хорошо: Проверка без исключений ≈ 0.01 ms
for (int i = 0; i < 10_000; i++)
{
    if (i < 0) { /* Невозможно */ }
}
```

---

## **Минимизация влияния на память**

1. **Избегай исключений для обычной логики**:

   ```csharp
   // Плохо
   try { return int.Parse(input); }
   catch { return 0; }

   // Хорошо
   if (int.TryParse(input, out int result))
       return result;
   else
       return 0;
   ```

2. **Кэшируй часто используемые исключения**:

   ```csharp
   private static readonly Exception ConnectionException =
       new InvalidOperationException("Connection failed");

   void Connect()
   {
       if (/* ошибка */)
           throw ConnectionException;
   }
   ```

3. **Используй `ExceptionDispatchInfo`**:

   ```csharp
   ExceptionDispatchInfo.Capture(ex).Throw();
   ```

---

## **Альтернатива исключениям: Паттерн Result**

**Result-объект** инкапсулирует результат операции и ошибку:

```csharp
public class Result<T>
{
    public T Value { get; }
    public string Error { get; }
    public bool IsSuccess => Error == null;

    private Result(T value, string error)
    {
        Value = value;
        Error = error;
    }

    public static Result<T> Success(T value) => new Result<T>(value, null);
    public static Result<T> Failure(string error) => new Result<T>(default, error);
}

// Использование
Result<int> ParseNumber(string input)
{
    if (int.TryParse(input, out int number))
        return Result<int>.Success(number);
    else
        return Result<int>.Failure("Неверный формат числа");
}

var result = ParseNumber("123");
if (result.IsSuccess)
{
    Console.WriteLine($"Число: {result.Value}");
}
else
{
    Console.WriteLine($"Ошибка: {result.Error}");
}
```

**Преимущества**:

- Нет накладных расходов на исключения
- Явная обработка ошибок
- Поддержка множественных ошибок

**Недостатки**:

- Усложнение кода
- Не подходит для фатальных ошибок

---

## **Best Practices: Золотые правила работы с исключениями**

1. **Не используй исключения для контроля потока**

   ```csharp
   // Плохо
   try { while(true) list.GetNext(); }
   catch { /* Конец списка */ }

   // Хорошо
   foreach (var item in list) { ... }
   ```

2. **Перехватывай конкретные типы исключений**

   ```csharp
   catch (SqlException ex) { ... } // Вместо общего Exception
   ```

3. **Всегда очищай ресурсы**

   ```csharp
   using (var resource = new DisposableResource())
   {
       // Работа с ресурсом
   }
   ```

4. **Логируй правильно**

   ```csharp
   catch (Exception ex)
   {
       logger.LogError(ex, "Ошибка при обработке заказа");
       throw; // Повторный throw после логирования
   }
   ```

5. **Документируй исключения**

   ```csharp
   /// <exception cref="InvalidOperationException">Если объект не инициализирован</exception>
   public void Process()
   {
       if (!initialized)
           throw new InvalidOperationException("Объект не инициализирован");
   }
   ```

---

## **Заключение: Когда что использовать**

| Ситуация                                          | Подход                 |
| ------------------------------------------------- | ---------------------- |
| **Фатальные ошибки** (OutOfMemory, StackOverflow) | Исключения             |
| **Ожидаемые ошибки** (Валидация ввода)            | Result-паттерн         |
| **Внешние ресурсы** (Файлы, сеть)                 | Исключения + try-catch |
| **Высокопроизводительный код**                    | Result + проверки      |
| **Многопоточность**                               | Исключения + агрегация |

**Итоговые рекомендации**:

- Используй исключения для **непредвиденных** ситуаций
- Применяй Result для **ожидаемых** ошибок бизнес-логики
- Всегда документируй возможные исключения
- Профилируй код на предмет избыточных исключений
- Сочетай подходы для оптимального результата

Помни: "Лучшее исключение — то, которое никогда не было выброшено". Следуя этим принципам, ты сможете создавать надежные и производительные приложения на C#.
