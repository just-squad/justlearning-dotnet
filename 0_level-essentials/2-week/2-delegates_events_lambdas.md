# **Делегаты, события, лямбда-выражения**

**Цель**: Понять, как создавать гибкие системы с обратными вызовами.

## **Делегаты**

Делегат — это "указатель" на метод. Пример: система уведомлений.

```csharp
// Объявление делегата
public delegate void Notify(string message);

class NotificationService
{
    // Событие на основе делегата
    public event Notify? OnMessageSent;

    public void SendEmail(string email)
    {
        // Отправка email...
        OnMessageSent?.Invoke($"Email отправлен на {email}"); // Вызов события
    }
}

// Использование
var service = new NotificationService();
service.OnMessageSent += (msg) => Console.WriteLine(msg); // Подписка через лямбду
service.SendEmail("user@example.com");
```

## **Лямбда-выражения**

Сокращенная запись методов. Пример сортировки списка:

```csharp
List<int> numbers = new List<int> { 5, 1, 4, 2 };
numbers.Sort((a, b) => a.CompareTo(b)); // Лямбда для сравнения
// Результат: [1, 2, 4, 5]
```
