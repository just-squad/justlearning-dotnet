# **4. Логирование в Elasticsearch + Kibana**

**Цель**: Агрегировать и искать логи из распределенных систем.

## **Настройка**

1. Запустите Elasticsearch и Kibana через Docker:

   ```yaml
   # docker-compose.yml
   elasticsearch:
     image: elasticsearch:8.5.0
     environment:
       - discovery.type=single-node
     ports:
       - '9200:9200'

   kibana:
     image: kibana:8.5.0
     ports:
       - '5601:5601'
   ```

2. Установите пакет для записи логов в Elasticsearch:

   ```bash
   Serilog.Sinks.Elasticsearch
   ```

3. Настройте Serilog в `Program.cs`:

   ```csharp
   Log.Logger = new LoggerConfiguration()
       .WriteTo.Elasticsearch(new ElasticsearchSinkOptions(new Uri("http://localhost:9200"))
       {
           AutoRegisterTemplate = true,
           IndexFormat = "myapp-logs-{0:yyyy.MM}"
       })
       .CreateLogger();
   ```

4. В Kibana (<http://localhost:5601>) создайте индекс `myapp-logs-*` и исследуйте логи через Discover.
