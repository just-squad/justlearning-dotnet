# **Мониторинг с Prometheus + Grafana**

**Цель**: Видеть метрики приложения (запросы в секунду, ошибки, время ответа).

## **Настройка Prometheus**

1. Установите пакет:

   ```bash
   prometheus-net.AspNetCore
   ```

2. Добавьте middleware в `Program.cs`:

   ```csharp
   app.UseHttpMetrics(); // Сбор метрик HTTP
   app.UseMetricServer(); // endpoint "/metrics"
   ```

3. Запустите Prometheus в Docker:

   ```yaml
   # docker-compose.yml
   version: '3'
   services:
     prometheus:
       image: prom/prometheus
       ports:
         - '9090:9090'
       volumes:
         - ./prometheus.yml:/etc/prometheus/prometheus.yml
   ```

   **prometheus.yml**:

   ```yaml
   scrape_configs:
     - job_name: 'myapi'
       static_configs:
         - targets: ['host.docker.internal:5000'] # Адрес вашего API
   ```

## **Визуализация в Grafana**

1. Запустите Grafana:

   ```yaml
   # docker-compose.yml
   grafana:
     image: grafana/grafana
     ports:
       - '3000:3000'
   ```

2. Настройте источник данных Prometheus в Grafana (<http://prometheus:9090>).
3. Создайте дашборд с графиками:
   - HTTP-запросы в секунду.
   - Среднее время ответа.
   - Количество ошибок 5xx.
