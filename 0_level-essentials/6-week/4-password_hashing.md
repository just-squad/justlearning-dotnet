# **Хеширование паролей**

**Цель**: Не хранить пароли в открытом виде.

## **Использование BCrypt**

1. Установите пакет:

   ```bash
   BCrypt.Net-Next
   ```

2. Хеширование при регистрации:

   ```csharp
   string passwordHash = BCrypt.Net.BCrypt.HashPassword("пароль");
   ```

3. Проверка при входе:

   ```csharp
   bool isPasswordValid = BCrypt.Net.BCrypt.Verify("введенный-пароль", storedHash);
   ```
