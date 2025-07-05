# **OAuth 2.0 (авторизация через Google/Facebook)**

**Цель**: Разрешить пользователям входить через соцсети.

## **Настройка Google OAuth**

1. Получите `Client ID` и `Client Secret` в [Google Cloud Console](https://console.cloud.google.com/).
2. Установите пакет:

   ```bash
   Microsoft.AspNetCore.Authentication.Google
   ```

3. Настройте в `Program.cs`:

   ```csharp
   builder.Services.AddAuthentication()
       .AddGoogle(options =>
       {
           options.ClientId = "ваш-client-id";
           options.ClientSecret = "ваш-client-secret";
       });
   ```

4. Для входа через Google используйте URL: `/signin-google`.
