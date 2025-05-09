# **Git: ветвление, merge, pull requests**

**Цель**: Научиться работать в команде и управлять версиями кода.

---

## **Основные команды**

1. **Создание ветки**:

   ```bash
   git checkout -b feature/add-authentication
   ```

2. **Коммит изменений**:

   ```bash
   git add .
   git commit -m "Добавлена аутентификация через JWT"
   ```

3. **Пушить ветку в удаленный репозиторий**:

   ```bash
   git push origin feature/add-authentication
   ```

4. **Слияние веток** (через Pull Request в GitHub/GitLab):
   - Перейдите в репозиторий → создайте PR из `feature/add-authentication` в `main`.
   - Проведите код-ревью → разрешите конфликты (если есть) → выполните merge.

---

## **Стратегии ветвления**

- **Git Flow**:

  - `main` — стабильная версия.
  - `develop` — текущая разработка.
  - `feature/*` — новые фичи.
  - `hotfix/*` — срочные исправления.

- **Пример workflow**:

  ```bash
  git checkout develop
  git pull origin develop
  git checkout -b feature/new-endpoint
  # Пишите код...
  git push origin feature/new-endpoint
  ```
