# Работа с файлами и исключения

**Цель**: Читать и записывать данные в файлы, обрабатывать ошибки.

## **Запись в файл**

```csharp
using System.IO;

string path = "test.txt";
File.WriteAllText(path, "Привет, мир!"); // Создаст файл и запишет текст
```

## Чтение из файла

```csharp
string content = File.ReadAllText(path); // Прочитает "Привет, мир!"
```
