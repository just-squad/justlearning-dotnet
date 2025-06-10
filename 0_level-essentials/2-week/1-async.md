# **Асинхронность: `async/await`, `Task`**

---

## **Основы async/await: Зачем они нужны?**

**Асинхронность** — это подход, позволяющий выполнять операции без блокировки текущего потока. Ключевые слова `async` и `await` упрощают написание асинхронного кода, делая его похожим на синхронный.

**Пример из жизни**:  
Представьте, что вы готовите завтрак:

- Поставить чайник (асинхронная операция — ждем, пока закипит, но не стоим рядом)
- Пока чайник кипятится, нарезать хлеб (параллельная задача)
- Когда чайник закипел — заварить чай (продолжение)

```csharp
async Task MakeBreakfastAsync()
{
    Task<bool> kettleTask = BoilKettleAsync();
    CutBread(); // Синхронная операция

    bool isBoiled = await kettleTask; // "Ждем" результат без блокировки потока
    if (isBoiled)
    {
        await MakeTeaAsync(); // Продолжаем после завершения
    }
}
```

---

## **Как работает async/await внутри: Машина состояний**

Когда компилятор встречает `async` метод, он генерирует **класс-машину состояний**, который:

1. Запоминает текущее состояние выполнения
2. Управляет продолжением выполнения после `await`
3. Хранит локальные переменные и параметры

**Пример преобразования**:
Исходный код:

```csharp
async Task MyAsyncMethod()
{
    Console.WriteLine("Start");
    await Task.Delay(1000);
    Console.WriteLine("After first await");
    await Task.Delay(1000);
    Console.WriteLine("End");
}
```

Во что это превращается (упрощенно). Компилятор генерирует примерно такой код:

```csharp
class StateMachine
{
    private int _state = 0;
    private TaskAwaiter _awaiter;
    private MyAsyncMethodContext _context;

    public void MoveNext()
    {
        switch (_state)
        {
            case 0:
                Console.WriteLine("Start");
                _awaiter = Task.Delay(1000).GetAwaiter();
                if (!_awaiter.IsCompleted)
                {
                    _state = 1;
                    _awaiter.OnCompleted(MoveNext);
                    return;
                }
                goto case 1;
            case 1:
                _awaiter.GetResult();
                Console.WriteLine("After first await");
                _awaiter = Task.Delay(1000).GetAwaiter();
                if (!_awaiter.IsCompleted)
                {
                    _state = 2;
                    _awaiter.OnCompleted(MoveNext);
                    return;
                }
                goto case 2;
            case 2:
                _awaiter.GetResult();
                Console.WriteLine("End");
                break;
        }
    }
}
```

Шаги выполнения:

1. Инициализация:
   - Создается объект StateMachine.
   - \_state = 0 (начальное состояние).
2. Первый вызов MoveNext():
    - Выполняется код до первого await.
    - Запускается Task.Delay(1000).
    - Если задача не завершена сразу:
        - \_state меняется на 1.
        - Регистрируется колбэк MoveNext для продолжения.
        - Управление возвращается вызывающему коду.
3. После завершения первой задержки:
    - Вызывается MoveNext() (через колбэк).
    - \_state равен 1.
    - Выполняется код после первого await.
    - Запускается второй Task.Delay(1000).
    - Если задача не завершена:
        - \_state = 2.
        - Регистрируется колбэк MoveNext.
4. После второй задержки:
    - MoveNext() вызывается снова.
    - \_state = 2.
    - Выполняется код после второго await.
    - Метод завершается.

---

## **Потоки (Threads) vs Задачи (Tasks)**

### Ключевые различия

| Характеристика     | Поток (Thread)          | Задача (Task)       |
| ------------------ | ----------------------- | ------------------- |
| Уровень абстракции | Низкий (ОС)             | Высокий (TPL)       |
| Пул потоков        | Не из пула              | Использует пул      |
| Ресурсы            | Дорогое создание (~1MB) | Легковесные         |
| Управление         | Вручную                 | Через TaskScheduler |
| Возврат результата | Нет                     | Да (Task\<T>)       |

**Пример создания**:

```csharp
// Поток
var thread = new Thread(() => DoWork());
thread.Start();

// Задача
var task = Task.Run(() => DoWork());
```

---

## **Task Scheduler: Диспетчер задач**

**TaskScheduler** определяет, как и где выполняются задачи:

- `ThreadPoolTaskScheduler` (по умолчанию) — использует пул потоков
- `SynchronizationContextTaskScheduler` — для UI-потоков (WPF/WinForms)
- Кастомные планировщики (например, для ограничения параллелизма)

**Пример с UI**:

```csharp
async void Button_Click(object sender, EventArgs e)
{
    // Запуск в фоновом потоке
    await Task.Run(() => HeavyComputation());

    // Автоматическое возвращение в UI-поток
    UpdateUI();
}
```

---

## **Производительность: Task vs Thread**

**Пул потоков**:

- Изначально содержит минимальное количество потоков
- Автоматически масштабируется при нагрузке
- Переиспользует потоки, уменьшая накладные расходы

**Сравнение**:

```csharp
// 1000 потоков
for (int i = 0; i < 1000; i++)
{
    new Thread(() => Thread.Sleep(100)).Start(); // ~2GB памяти, медленно
}

// 1000 задач
Parallel.For(0, 1000, i =>
{
    Task.Delay(100).Wait(); // ~1MB памяти, быстро
});
```

**Результаты**:

- Создание 1000 потоков: ~2 сек, ~2GB RAM
- Создание 1000 задач: ~0.1 сек, ~1MB RAM

---

## **Важные нюансы асинхронности**

### async void — только для событий

```csharp
// Плохо (исключения не перехватываются)
async void BadMethod() { throw new Exception(); }

// Хорошо
async Task GoodMethod() { ... }
```

### ConfigureAwait(false)

```csharp
async Task GetDataAsync()
{
    var data = await FetchDataAsync().ConfigureAwait(false);
    // Не нужен контекст синхронизации
    ProcessData(data); // Может выполняться в потоке из пула
}
```

### Deadlock-опасности

**Плохой код**:

```csharp
async Task GetData()
{
    var result = FetchDataAsync().Result; // Блокирующий вызов
}

// Вызовет deadlock в UI-потоке!
```

**Хороший код**:

```csharp
async Task GetData()
{
    var result = await FetchDataAsync(); // Не блокирует поток
}
```

---

## **Шаблоны проектирования для асинхронности**

### Отмена операций (CancellationToken)

```csharp
async Task LongOperationAsync(CancellationToken token)
{
    while (!token.IsCancellationRequested)
    {
        await Task.Delay(1000, token);
        // Работа
    }
}

// Использование
var cts = new CancellationTokenSource();
var task = LongOperationAsync(cts.Token);
cts.CancelAfter(5000); // Отмена через 5 сек
```

### Ограничение параллелизма

```csharp
var semaphore = new SemaphoreSlim(5); // Макс 5 параллельных задач

async Task ProcessItemAsync(int item)
{
    await semaphore.WaitAsync();
    try
    {
        await ProcessAsync(item);
    }
    finally
    {
        semaphore.Release();
    }
}
```

---

## **Когда использовать асинхронность?**

1. **I/O-bound операции**:

   - Работа с файлами
   - Сетевые запросы
   - Базы данных

2. **CPU-bound операции** (с осторожностью):

   ```csharp
   await Task.Run(() => HeavyComputation()); // Выполнение в фоне
   ```

3. **UI-приложения**:
   - Поддержание отзывчивости интерфейса

---

## **Лучшие практики**

1. **Избегай async void** (кроме обработчиков событий)
2. **Не блокируй async код** (никаких .Result/Wait())
3. **Используй ConfigureAwait(false)** в библиотеках
4. **Обрабатывай исключения**:

   ```csharp
   try
   {
       await SomeAsyncMethod();
   }
   catch (Exception ex)
   {
       // Логирование
   }
   ```

5. **Используй ValueTask** для высокопроизводительных сценариев:

   ```csharp
   public ValueTask<int> GetValueAsync()
   {
       if (cache.TryGetValue(out int value))
           return new ValueTask<int>(value);

       return new ValueTask<int>(FetchFromNetworkAsync());
   }
   ```

---

## **Заключение: Главные принципы асинхронности в C\#**

1. **async/await** — не создает потоки, а управляет выполнением задач
2. **Задачи** — абстракция над асинхронными операциями, не всегда связаны с потоками
3. **Пул потоков** — ключ к эффективности, не создавай потоки вручную
4. **Машина состояний** — волшебство компилятора, скрывающее сложность
5. **Не блокируйте** — используйте await "сверху донизу"
6. **Обработка ошибок** — критически важна для стабильности
7. **Профилируйте** — асинхронность не всегда означает производительность

Помни: "Асинхронный код пишется для _освобождения_ потоков, а не для их _создания_". Правильное использование async/await позволяет создавать высокопроизводительные и отзывчивые приложения, эффективно использующие ресурсы системы.
