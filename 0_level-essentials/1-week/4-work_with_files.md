# **Работа с файлами**

**Цель**: Читать и записывать данные в файлы, обрабатывать ошибки.

Работа с файлами — важная часть многих приложений. В C# для этого предоставляются мощные инструменты в пространстве имен `System.IO`.

## 1. Базовые операции с файлами

### Проверка существования файла

```csharp
string path = @"C:\example\test.txt";

if (File.Exists(path))
{
    Console.WriteLine("Файл существует");
}
else
{
    Console.WriteLine("Файл не найден");
}
```

### Создание и удаление файлов

```csharp
// Создание пустого файла
File.Create(@"C:\example\new_file.txt").Close();

// Удаление файла
File.Delete(path);
```

### Копирование и перемещение

```csharp
string source = @"C:\example\source.txt";
string dest = @"C:\example\backup\copy.txt";

File.Copy(source, dest); // Копирование
File.Move(source, @"C:\example\new_location.txt"); // Перемещение
```

## 2. Работа с текстовыми файлами

### Чтение файла

```csharp
// Чтение всего содержимого
string content = File.ReadAllText(path);

// Построчное чтение
string[] lines = File.ReadAllLines(path);

// Чтение с кодировкой
string utfContent = File.ReadAllText(path, Encoding.UTF8);
```

### Запись в файл

```csharp
// Запись всего текста (перезапись)
File.WriteAllText(path, "Новый текст");

// Добавление текста в конец
File.AppendAllText(path, "\nДополнительный текст");

// Построчная запись
List<string> newLines = new List<string> { "Первая строка", "Вторая строка" };
File.WriteAllLines(path, newLines);
```

## 3. Работа с бинарными файлами

```csharp
byte[] data = { 0x48, 0x65, 0x6C, 0x6C, 0x6F }; // "Hello" в ASCII

// Запись бинарных данных
File.WriteAllBytes(@"C:\example\binary.bin", data);

// Чтение бинарных данных
byte[] readData = File.ReadAllBytes(@"C:\example\binary.bin");
```

## 4. Использование потоков (Streams)

### StreamReader/StreamWriter

```csharp
using (StreamWriter writer = new StreamWriter(path))
{
    writer.WriteLine("Первая строка");
    writer.WriteLine("Вторая строка");
}

using (StreamReader reader = new StreamReader(path))
{
    string line;
    while ((line = reader.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

### FileStream для продвинутой работы

```csharp
using (FileStream fs = new FileStream(path, FileMode.Open, FileAccess.ReadWrite))
{
    byte[] buffer = new byte[1024];
    int bytesRead = fs.Read(buffer, 0, buffer.Length);

    // Работа с данными
    // ...

    fs.Write(buffer, 0, bytesRead);
}
```

## 5. Обработка исключений и подводные камни

### Распространенные исключения

- `FileNotFoundException` — файл не найден
- `DirectoryNotFoundException` — директория не существует
- `UnauthorizedAccessException` — нет прав доступа
- `IOException` — общие ошибки ввода-вывода

### Пример обработки

```csharp
try
{
    string content = File.ReadAllText(path);
}
catch (FileNotFoundException ex)
{
    Console.WriteLine($"Ошибка: {ex.Message}");
}
catch (UnauthorizedAccessException ex)
{
    Console.WriteLine($"Нет прав доступа: {ex.Message}");
}
catch (IOException ex)
{
    Console.WriteLine($"Ошибка ввода-вывода: {ex.Message}");
}
```

### Частые проблемы

1. **Блокировка файлов**: Всегда освобождай ресурсы с помощью `using`
2. **Кодировки**: Явно указывай кодировку (по умолчанию UTF-8 без BOM)
3. **Пути файлов**: Используй `Path.Combine()` для создания cross-platform путей до файлов и папок.

```csharp
string safePath = Path.Combine("folder", "subfolder", "file.txt");
```

## 6. Производительность и лучшие практики

### Лайфхаки для оптимизации

1. **Буферизация** для частых операций записи:

```csharp
using (BufferedStream bs = new BufferedStream(new FileStream(path, FileMode.Create)))
{
    byte[] data = Encoding.UTF8.GetBytes("Большие данные...");
    bs.Write(data, 0, data.Length);
}
```

1. **Асинхронные операции** для больших файлов:

```csharp
async Task ReadFileAsync()
{
    using (StreamReader reader = new StreamReader(path))
    {
        string content = await reader.ReadToEndAsync();
    }
}
```

1. **Работа с большими файлами** по частям:

```csharp
int bufferSize = 4096;
byte[] buffer = new byte[bufferSize];

// using высвобождает созданные ресурсы. Высвобождаются по выходу из области видимости определенной блоком {} 
using (FileStream fs = new FileStream(path, FileMode.Open))
{
    int bytesRead;
    while ((bytesRead = fs.Read(buffer, 0, bufferSize)) > 0)
    {
        // Обработка части файла
    }
}
```

1. **Используй FileInfo** для частых операций с одним файлом:

```csharp
FileInfo fileInfo = new FileInfo(path);
if (fileInfo.Exists)
{
    Console.WriteLine($"Размер файла: {fileInfo.Length} байт");
}
```

## 7. Расширенные возможности

### Временные файлы

```csharp
string tempFile = Path.GetTempFileName();
using (StreamWriter sw = File.CreateText(tempFile))
{
    sw.WriteLine("Временные данные");
}
File.Delete(tempFile); // Не забудьте удалить!
```

### Мониторинг изменений файлов

```csharp
FileSystemWatcher watcher = new FileSystemWatcher
{
    Path = @"C:\example",
    Filter = "*.txt"
};

watcher.Changed += (s, e) => Console.WriteLine($"Файл изменен: {e.Name}");
watcher.EnableRaisingEvents = true;
```

### Работа с атрибутами файлов

```csharp
File.SetAttributes(path, FileAttributes.Hidden | FileAttributes.ReadOnly);
File.SetCreationTime(path, DateTime.Now.AddDays(-1));
```

## Заключение: Главные правила работы с файлами

1. Всегда используй `using` для гарантированного освобождения ресурсов
2. Обрабатывай все возможные исключения
3. Указывай кодировку явно при работе с текстом
4. Для больших файлов используй потоковую обработку
5. Проверяй существование файлов и директорий перед операциями
6. Используй асинхронные методы для улучшения производительности
7. Будь осторожны с путями — используйте `Path.Combine()`

Пример комплексного использования:

```csharp
try
{
    string logPath = Path.Combine("logs", "app.log");

    if (!Directory.Exists("logs"))
    {
        Directory.CreateDirectory("logs");
    }

    await File.AppendAllTextAsync(logPath, $"{DateTime.Now}: Запуск приложения\n");

    using (var fs = new FileStream(logPath, FileMode.Open, FileAccess.Read))
    {
        using (var reader = new StreamReader(fs))
        {
            string logContent = await reader.ReadToEndAsync();
            Console.WriteLine($"Лог содержит {logContent.Length} символов");
        }
    }
}
catch (Exception ex)
{
    Console.WriteLine($"Ошибка: {ex.Message}");
}
```
