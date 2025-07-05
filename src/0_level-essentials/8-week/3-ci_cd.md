# **CI/CD: настройка пайплайна**

**Цель**: Автоматизировать тестирование и деплой.

---

## **GitHub Actions**

Создайте файл `.github/workflows/build.yml`:

```yaml
name: Build and Test
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: 7.0.x

      - name: Build
        run: dotnet build --configuration Release

      - name: Run tests
        run: dotnet test
```

---

## **Деплой в Azure**

1. Установите [Azure CLI](https://docs.microsoft.com/ru-ru/cli/azure/install-azure-cli).
2. Создайте App Service:

   ```bash
   az group create --name MyResourceGroup --location eastus
   az appservice plan create --name MyPlan --resource-group MyResourceGroup --sku B1
   az webapp create --name MyApi --resource-group MyResourceGroup --plan MyPlan --runtime "DOTNETCORE:7.0"
   ```

3. Настройте GitHub Actions для деплоя:

   ```yaml
   - name: Deploy to Azure
     uses: azure/webapps-deploy@v2
     with:
       app-name: MyApi
       publish-profile: ${{ secrets.AZURE_PUBLISH_PROFILE }}
   ```
