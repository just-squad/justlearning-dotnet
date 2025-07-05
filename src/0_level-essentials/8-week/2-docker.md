# **Docker: контейнеризация приложения**

**Цель**: Упаковать приложение в контейнер для простого развертывания.

---

## **Dockerfile**

Создайте файл в корне проекта:

```dockerfile
# Шаг 1: Базовый образ
FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
WORKDIR /src

# Шаг 2: Копируем и собираем проект
COPY *.csproj .
RUN dotnet restore
COPY . .
RUN dotnet publish -c Release -o /app

# Шаг 3: Финальный образ
FROM mcr.microsoft.com/dotnet/aspnet:7.0
WORKDIR /app
COPY --from=build /app .
ENTRYPOINT ["dotnet", "MyApi.dll"]
```

---

## **Запуск контейнера**

1. Соберите образ:

   ```bash
   docker build -t myapi .
   ```

2. Запустите контейнер:

   ```bash
   docker run -d -p 5000:80 --name myapi-container myapi
   ```

3. Проверьте:

   ```bash
   curl http://localhost:5000/api/posts
   ```

---

## **Docker Compose для БД и приложения**

`docker-compose.yml`:

```yaml
version: '3'
services:
  myapi:
    build: .
    ports:
      - '5000:80'
    depends_on:
      - db
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
volumes:
  postgres_data:
```

Запуск:

```bash
docker-compose up -d
```
