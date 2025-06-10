# **Введение в SOLID**

**Цель**: Писать код, который легко поддерживать и расширять.

---

## **Принципы SOLID**

1. **Single Responsibility (Единая ответственность)**  
   Класс должен решать только одну задачу.

   **Плохо**:

   ```csharp
   class ReportManager
   {
       public void GenerateReport() { /* ... */ }
       public void SaveToFile() { /* ... */ } // Нарушение: две ответственности!
   }
   ```

   **Хорошо**:

   ```csharp
   class ReportGenerator { /* Только генерация */ }
   class FileSaver { /* Только сохранение */ }
   ```

2. **Open-Closed (Открытость/закрытость)**  
   Класс должен быть открыт для расширения, но закрыт для изменений.

   Пример с интерфейсом:

   ```csharp
   interface IShape { double Area(); }

   class Circle : IShape { /* Реализация Area */ }
   class Square : IShape { /* Реализация Area */ }

   // Новые фигуры добавляются без изменения существующего кода.
   ```
