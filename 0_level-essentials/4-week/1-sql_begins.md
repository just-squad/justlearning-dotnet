# **Основы SQL**

**Цель**: Научиться писать простые SQL-запросы для работы с данными.

## **Таблицы и данные**

Представьте, что база данных — это Excel-файл с листами (таблицами). Каждая таблица хранит данные в строках и столбцах.  
Пример таблицы `Users`:

| Id  | Name | Email            |
| --- | ---- | ---------------- |
| 1   | Анна | <anna@example.com> |
| 2   | Иван | <ivan@test.com>    |

---

## **Основные операции**

1. **SELECT**: Получить данные.

   ```sql
   -- Все пользователи
   SELECT * FROM Users;

   -- Только имя и email
   SELECT Name, Email FROM Users;

   -- Фильтрация по условию
   SELECT * FROM Users WHERE Id = 1;
   ```

2. **INSERT**: Добавить новую запись.

   ```sql
   INSERT INTO Users (Name, Email) VALUES ('Мария', 'maria@test.com');
   ```

3. **JOIN**: Объединить данные из двух таблиц.  
   Пример таблицы `Orders`:

   | OrderId | UserId | Product |
   | ------- | ------ | ------- |
   | 101     | 1      | Книга   |
   | 102     | 2      | Ноутбук |

   Запрос с объединением:

   ```sql
   SELECT Users.Name, Orders.Product
   FROM Users
   INNER JOIN Orders ON Users.Id = Orders.UserId;
   ```

   Результат:

   | Name | Product |
   | ---- | ------- |
   | Анна | Книга   |
   | Иван | Ноутбук |

4. **Транзакции**: Группа операций, которые выполняются как единое целое (либо все, либо ничего).  
   Пример: перевод денег между счетами.

   ```sql
   BEGIN TRANSACTION;
   UPDATE Accounts SET Balance = Balance - 100 WHERE Id = 1;
   UPDATE Accounts SET Balance = Balance + 100 WHERE Id = 2;
   COMMIT; -- Если ошибок нет
   -- ROLLBACK; -- Если что-то пошло не так
   ```
