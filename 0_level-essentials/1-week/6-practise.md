# Практика на неделю

1. **Калькулятор**:  
   Консольное приложение, которое принимает два числа и оператор (`+`, `-`, `*`, `/`), затем выводит результат.

2. **Менеджер задач**:  
   Список дел с возможностью добавления, удаления и вывода всех задач.

3. **Мини-проект "Угадай число"**:
   - Программа загадывает число от 1 до 100.
   - Пользователь вводит предположение.
   - Программа подсказывает "больше" или "меньше".
   - После угадывания выводится количество попыток и результат сохраняется в файл `results.txt`.

**Пример кода для игры**:

```csharp
Random random = new Random();
int secretNumber = random.Next(1, 101);
int attempts = 0;
Console.WriteLine("Угадайте число от 1 до 100!");

while (true)
{
    Console.Write("Ваш вариант: ");
    int guess = int.Parse(Console.ReadLine());
    attempts++;

    if (guess == secretNumber)
    {
        Console.WriteLine($"Поздравляем! Вы угадали за {attempts} попыток.");
        File.WriteAllText("results.txt", $"Попыток: {attempts}, Число: {secretNumber}");
        break;
    }
    else if (guess < secretNumber)
    {
        Console.WriteLine("Больше!");
    }
    else
    {
        Console.WriteLine("Меньше!");
    }
}
```
