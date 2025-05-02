# Исключения

Обработка ошибок (например, если файл не найден).

```csharp
try
{
    string content = File.ReadAllText("missing_file.txt");
}
catch (FileNotFoundException ex)
{
    Console.WriteLine("Файл не найден!");
}
```
