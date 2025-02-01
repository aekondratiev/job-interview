**Horizontal Pod Autoscaler (HPA)** в Kubernetes — это механизм автоматического масштабирования количества Pod'ов в Deployment, ReplicaSet или StatefulSet на основе наблюдаемой нагрузки (например, CPU, памяти или пользовательских метрик). HPA позволяет приложению автоматически адаптироваться к изменяющейся нагрузке, увеличивая или уменьшая количество реплик Pod'ов.

### Как работает HPA?

1. **Мониторинг метрик**:
   - HPA собирает метрики из Pod'ов (например, использование CPU или памяти) или пользовательские метрики (например, количество запросов в секунду).

2. **Сравнение с целевыми значениями**:
   - HPA сравнивает текущие значения метрик с целевыми значениями, указанными в конфигурации.

3. **Масштабирование**:
   - Если текущие значения метрик превышают целевые, HPA увеличивает количество реплик Pod'ов.
   - Если текущие значения метрик ниже целевых, HPA уменьшает количество реплик Pod'ов.

4. **Обновление статуса**:
   - HPA обновляет статус масштабируемого ресурса (например, Deployment), изменяя количество реплик.

### Пример манифеста HPA

Пример HPA, который масштабирует Deployment на основе использования CPU:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

### Объяснение полей:

- **`apiVersion`**: Указывает версию API (обычно `autoscaling/v2`).
- **`kind`**: Тип объекта — `HorizontalPodAutoscaler`.
- **`metadata`**: Метаданные, такие как имя HPA.
- **`spec`**: Основная конфигурация HPA.
  - **`scaleTargetRef`**: Указывает масштабируемый ресурс (например, Deployment).
    - **`apiVersion`**: Версия API ресурса.
    - **`kind`**: Тип ресурса (например, `Deployment`).
    - **`name`**: Имя ресурса.
  - **`minReplicas`**: Минимальное количество реплик.
  - **`maxReplicas`**: Максимальное количество реплик.
  - **`metrics`**: Метрики, на основе которых выполняется масштабирование.
    - **`type`**: Тип метрики (например, `Resource` для CPU/памяти или `Pods`/`Object` для пользовательских метрик).
    - **`resource`**: Конфигурация для метрик ресурсов.
      - **`name`**: Имя ресурса (например, `cpu` или `memory`).
      - **`target`**: Целевое значение метрики.
        - **`type`**: Тип целевого значения (например, `Utilization` для процента использования).
        - **`averageUtilization`**: Целевой процент использования (например, 50%).

### Пример с пользовательскими метриками

HPA также поддерживает пользовательские метрики. Например, масштабирование на основе количества запросов в секунду:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Pods
      pods:
        metric:
          name: requests-per-second
        target:
          type: AverageValue
          averageValue: 500
```

### Как создать HPA?

1. **Создание HPA**:
   Примените манифест HPA к кластеру:

   ```bash
   kubectl apply -f my-app-hpa.yaml
   ```

2. **Проверка статуса HPA**:
   Проверьте статус HPA:

   ```bash
   kubectl get hpa my-app-hpa
   ```

   Пример вывода:

   ```
   NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
   my-app-hpa   Deployment/my-app       50%/50%   2         10        4          5m
   ```

   - **`TARGETS`**: Текущие значения метрик и целевые значения.
   - **`REPLICAS`**: Текущее количество реплик.

### Преимущества использования HPA

1. **Автоматическое масштабирование**: Автоматически адаптирует количество Pod'ов к нагрузке.
2. **Эффективность использования ресурсов**: Уменьшает количество Pod'ов при низкой нагрузке, что позволяет экономить ресурсы.
3. **Гибкость**: Поддержка различных метрик (CPU, память, пользовательские метрики).
4. **Интеграция с Kubernetes**: Полная интеграция с другими компонентами Kubernetes.

### Ограничения

- **Задержка масштабирования**: Масштабирование может занимать некоторое время, так как HPA периодически проверяет метрики.
- **Требует настройки метрик**: Для работы HPA необходимо настроить сбор метрик (например, с помощью Metrics Server или Prometheus).
- **Ограничения на минимальное и максимальное количество реплик**: Нельзя масштабировать ниже `minReplicas` или выше `maxReplicas`.

### Когда использовать HPA?

- Для приложений с переменной нагрузкой, где количество запросов может значительно изменяться.
- Для обеспечения высокой доступности и производительности приложения.
- Для оптимизации использования ресурсов кластера.

### Пример с Deployment

HPA часто используется вместе с Deployment. Например:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: my-app:1.0.0
          resources:
            requests:
              cpu: "500m"
              memory: "512Mi"
            limits:
              cpu: "1000m"
              memory: "1024Mi"
---
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

В этом примере:
- Deployment создает 3 реплики приложения.
- HPA масштабирует количество реплик от 2 до 10 на основе использования CPU.

### Заключение

Horizontal Pod Autoscaler — это мощный инструмент для автоматического масштабирования приложений в Kubernetes. Он позволяет приложениям адаптироваться к изменяющейся нагрузке, обеспечивая высокую доступность и эффективное использование ресурсов.

