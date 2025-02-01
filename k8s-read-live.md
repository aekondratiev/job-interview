В Kubernetes (k8s) пробы (probes) используются для мониторинга состояния подов (pods) и обеспечения их корректной работы. Существует два основных типа проб: **liveness probe** (проба жизнеспособности) и **readiness probe** (проба готовности). Они помогают Kubernetes принимать решения о перезапуске подов или их включении в балансировку нагрузки.

### 1. **Liveness Probe (проба жизнеспособности)**
Liveness probe проверяет, работает ли контейнер в поде. Если проба завершается неудачно, Kubernetes считает, что контейнер "мертв", и перезапускает его. Это полезно для обработки ситуаций, когда приложение зависло или находится в неработоспособном состоянии, но процесс продолжает работать.

#### Пример использования:
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 10
```

- **httpGet**: Проба отправляет HTTP GET-запрос по указанному пути и порту. Если возвращается код ответа 200-399, проба считается успешной.
- **initialDelaySeconds**: Задержка перед первой проверкой (в секундах). Это позволяет приложению инициализироваться.
- **periodSeconds**: Интервал между проверками (в секундах).

#### Другие типы liveness probe:
- **exec**: Выполняет команду внутри контейнера. Если команда завершается с кодом 0, проба считается успешной.
  ```yaml
  livenessProbe:
    exec:
      command:
        - cat
        - /tmp/healthy
  ```
- **tcpSocket**: Проверяет доступность TCP-порта.
  ```yaml
  livenessProbe:
    tcpSocket:
      port: 8080
  ```

---

### 2. **Readiness Probe (проба готовности)**
Readiness probe проверяет, готов ли контейнер принимать трафик. Если проба завершается неудачно, Kubernetes исключает под из балансировки нагрузки (например, из Service). Это полезно, когда приложение временно не может обрабатывать запросы (например, во время инициализации или высокой нагрузки).

#### Пример использования:
```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

- **httpGet**: Аналогично liveness probe, отправляет HTTP GET-запрос.
- **initialDelaySeconds**: Задержка перед первой проверкой.
- **periodSeconds**: Интервал между проверками.

#### Другие типы readiness probe:
- **exec**: Выполняет команду внутри контейнера.
  ```yaml
  readinessProbe:
    exec:
      command:
        - check-ready.sh
  ```
- **tcpSocket**: Проверяет доступность TCP-порта.
  ```yaml
  readinessProbe:
    tcpSocket:
      port: 8080
  ```

---

### Основные параметры проб:
- **initialDelaySeconds**: Задержка перед первой проверкой.
- **periodSeconds**: Интервал между проверками.
- **timeoutSeconds**: Время ожидания ответа от пробы.
- **successThreshold**: Количество успешных проверок для перевода пробы в состояние "успешно".
- **failureThreshold**: Количество неудачных проверок для перевода пробы в состояние "неудача".

---

### Разница между liveness и readiness probe:
- **Liveness probe**:
  - Определяет, нужно ли перезапустить контейнер.
  - Если проба неудачна, контейнер будет перезапущен.
- **Readiness probe**:
  - Определяет, может ли контейнер принимать трафик.
  - Если проба неудачна, контейнер будет исключен из балансировки нагрузки.

---

### Пример полного манифеста с обеими пробами:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: my-container
    image: my-image
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
```

Этот пример показывает, как настроить liveness и readiness пробы для контейнера, чтобы Kubernetes мог эффективно управлять его состоянием.

