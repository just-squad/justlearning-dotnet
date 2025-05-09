# **Асинхронность: `async/await`, `Task`**

**Цель**: Научиться выполнять долгие операции (например, загрузку данных) без блокировки основного потока.

## **Аналогия из жизни**

Представьте, что вы готовите завтрак:

- **Синхронно**: Ждете, пока поджарится тост, и только потом начинаете жарить яйца. Это долго.
- **Асинхронно**: Ставите тост в тостер, и пока он готовится, начинаете жарить яйца. Экономите время.

## **Пример кода**

Метод для загрузки данных с веб-страницы:

```csharp
using System.Net.Http;

async Task<string> DownloadDataAsync(string url)
{
    HttpClient client = new HttpClient();
    string result = await client.GetStringAsync(url); // Не блокирует поток
    return result;
}

// Вызов
string data = await DownloadDataAsync("https://api.example.com/data");
Console.WriteLine(data);
```

**Важно**:

- Ключевые слова `async` (помечает метод как асинхронный) и `await` (ожидание завершения задачи без блокировки потока).
- Всегда используйте `async`-методы для I/O операций (запросы в сеть, работа с файлами).

## **Task**

Объект `Task` представляет собой асинхронную операцию. Пример:

```csharp
Task<int> LongCalculationAsync()
{
    return Task.Run(() =>
    {
        // Имитация долгого расчета
        Thread.Sleep(2000);
        return 42;
    });
}

// Использование
int result = await LongCalculationAsync();
```
