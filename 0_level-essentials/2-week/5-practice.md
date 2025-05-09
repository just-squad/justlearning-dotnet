# **Практика на неделю**

1. **Многопоточный парсер веб-страниц**:

   - Используйте `HttpClient` и `async/await`.
   - Параллельно обрабатывайте несколько URL.
   - Сохраняйте результаты в файл.

   **Пример**:

   ```csharp
   List<string> urls = new List<string> { "https://example.com/page1", "https://example.com/page2" };
   var tasks = urls.Select(url => DownloadDataAsync(url)).ToList();
   await Task.WhenAll(tasks); // Параллельное выполнение
   ```

2. **Приложение для скачивания файлов**:
   - Позволяет добавлять URL файлов в очередь.
   - Каждый файл скачивается асинхронно.
   - Отображает прогресс (например, `Downloading 5/10 files...`).
