# **Коллекции и LINQ**

**Цель**: Работать с наборами данных (списки, словари) и выполнять запросы.

## Список (`List<T>`)

Как динамический массив. Пример: список покупок.

```csharp
List<string> shoppingList = new List<string>();
shoppingList.Add("Хлеб");
shoppingList.Add("Молоко");
shoppingList.Remove("Хлеб");

foreach (string item in shoppingList)
{
    Console.WriteLine(item); // "Молоко"
}
```

## Словарь (`Dictionary<TKey, TValue>`)

Хранит пары "ключ-значение". Пример: телефонная книга.

```csharp
Dictionary<string, string> phoneBook = new Dictionary<string, string>();
phoneBook["Анна"] = "+375291234567";
phoneBook["Иван"] = "+375441234567";

Console.WriteLine(phoneBook["Анна"]); // Выведет номер Анны
```

## LINQ to Object

Язык запросов к коллекциям. Пример: найти все четные числа.

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0).ToList(); // [2, 4]
```
