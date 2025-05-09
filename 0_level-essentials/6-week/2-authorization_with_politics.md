# **Авторизация через роли и политики**

**Цель**: Ограничить доступ к методам API на основе ролей.

## **Роли**

Используйте атрибут `[Authorize(Roles = "Admin")]`:

```csharp
[Authorize(Roles = "Admin")]
[HttpDelete("users/{id}")]
public IActionResult DeleteUser(int id)
{
    // Только пользователи с ролью Admin могут удалять
}
```

## **Политики**

1. Зарегистрируйте политику в `Program.cs`:

   ```csharp
   builder.Services.AddAuthorization(options =>
   {
       options.AddPolicy("Age18+", policy =>
           policy.RequireAssertion(context =>
               context.User.HasClaim(c =>
                   c.Type == "Age" && int.Parse(c.Value) >= 18)));
   });
   ```

2. Примените политику к методу:

   ```csharp
   [Authorize(Policy = "Age18+")]
   [HttpGet("premium-content")]
   public IActionResult GetPremiumContent() { ... }
   ```
