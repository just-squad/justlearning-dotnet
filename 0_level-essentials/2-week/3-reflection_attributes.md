# **Рефлексия и атрибуты**

**Цель**: Научиться анализировать и модифицировать код во время выполнения.

## **Рефлексия**

Позволяет получить информацию о типах в runtime. Пример:

```csharp
class User
{
    public string Name { get; set; }
}

// Получение свойств класса User
Type userType = typeof(User);
foreach (var prop in userType.GetProperties())
{
    Console.WriteLine(prop.Name); // Выведет "Name"
}
```

## **Атрибуты**

Метки для классов или методов. Пример валидации:

```csharp
[AttributeUsage(AttributeTargets.Property)]
class RequiredAttribute : Attribute { }

class User
{
    [Required]
    public string Name { get; set; }
}

// Проверка атрибутов в runtime
User user = new User();
PropertyInfo prop = user.GetType().GetProperty("Name");
bool isRequired = prop.IsDefined(typeof(RequiredAttribute), false); // true
```
