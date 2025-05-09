# **Советы**

- **Используйте `ConfigureAwait(false)`** в библиотечных методах, чтобы избежать deadlock.
- **Не злоупотребляйте рефлексией** — это медленно и усложняет код.
- **SOLID — это не догма**, а инструмент. Начинайте с простых проектов, постепенно внедряя принципы.

---

## **Частые ошибки новичков**

1. **`async void` вместо `async Task`**:

   ```csharp
   // Плохо: исключения не будут перехвачены
   async void BadMethod() { ... }

   // Хорошо
   async Task GoodMethod() { ... }
   ```

2. **Блокировка асинхронного кода через `.Result`**:

   ```csharp
   // Плохо: может привести к deadlock
   string data = DownloadDataAsync(url).Result;

   // Хорошо
   string data = await DownloadDataAsync(url);
   ```
