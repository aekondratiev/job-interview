Horizontal Pod Autoscaler (HPA) и Vertical Pod Autoscaler (VPA) можно использовать вместе для оптимального масштабирования подов в Kubernetes.  

### Как использовать HPA и VPA одновременно  

#### 1. **Назначение HPA и VPA**  
- **HPA (Horizontal Pod Autoscaler)** — масштабирует поды **по количеству**, добавляя или удаляя их в зависимости от нагрузки (например, CPU, памяти, кастомных метрик).  
- **VPA (Vertical Pod Autoscaler)** — регулирует **ресурсы** (CPU и память) каждого пода, увеличивая или уменьшая их в зависимости от нагрузки.  

Использование их вместе позволяет добиться гибкого масштабирования:  
- VPA подбирает оптимальные ресурсы для пода.  
- HPA увеличивает или уменьшает количество подов в зависимости от нагрузки.  

---

### 2. **Настройка совместного использования**  

Так как HPA и VPA могут конфликтовать (оба могут изменять ресурсы пода), VPA нужно настроить так, чтобы он **не конфликтовал с HPA**.  

#### 2.1 **Настройка HPA**  
Пример HPA, который скейлит поды при загрузке CPU выше 50%:

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

> Важно: HPA изменяет **число реплик**, но не изменяет ресурсы контейнера.  

---

#### 2.2 **Настройка VPA**  
Чтобы VPA не мешал HPA, он **не должен изменять CPU requests**, иначе HPA не сможет правильно работать. Для этого указываем `updateMode: "Auto"` и **исключаем CPU**.  

Пример VPA, который изменяет только `memory`:

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: '*'
      controlledResources: ["memory"]
```

> Важно: Здесь **VPA изменяет только память** (`controlledResources: ["memory"]`), а CPU остается фиксированным, чтобы HPA корректно работал.  

---

### 3. **Вывод**  
1. **HPA** отвечает за **горизонтальное масштабирование** (количество подов).  
2. **VPA** отвечает за **вертикальное масштабирование**, но должен изменять **только память**, чтобы не мешать HPA.  
3. **Комбинируя их**, мы получаем эффективное использование ресурсов и масштабирование по нагрузке.  

Готов попробовать у себя? 🚀

Отлично! 🚀 Если у тебя уже есть кластер Kubernetes, вот шаги, чтобы всё развернуть:

### 1️⃣ **Установи VPA и HPA**
VPA не встроен в Kubernetes по умолчанию, его нужно установить:

```sh
kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vertical-pod-autoscaler.yaml
```

HPA уже есть в Kubernetes, но для работы с кастомными метриками может понадобиться **metrics-server**:

```sh
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Проверь, что `metrics-server` работает:

```sh
kubectl get deployment metrics-server -n kube-system
```

---

### 2️⃣ **Разверни тестовый деплоймент**
Создадим `Deployment`, который будем масштабировать:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: k8s.gcr.io/stress-ng:latest
        resources:
          requests:
            cpu: "100m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

Применим:

```sh
kubectl apply -f my-app-deployment.yaml
```

---

### 3️⃣ **Настроим HPA**
Создадим HPA, который будет добавлять поды при загрузке CPU выше 50%:

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

Применим:

```sh
kubectl apply -f my-app-hpa.yaml
```

---

### 4️⃣ **Настроим VPA**
VPA изменит **только память**, чтобы не конфликтовать с HPA:

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
    - containerName: '*'
      controlledResources: ["memory"]
```

Применим:

```sh
kubectl apply -f my-app-vpa.yaml
```

---

### 5️⃣ **Тестируем нагрузку**
Запустим нагрузку на CPU, чтобы проверить HPA:

```sh
kubectl run -i --tty --rm load-generator --image=busybox -- /bin/sh
```

Внутри контейнера выполни:

```sh
while true; do wget -q -O- http://my-app-service; done
```

Теперь можешь наблюдать, как HPA добавляет поды:

```sh
kubectl get hpa
kubectl get pods
```

Проверить рекомендации VPA можно так:

```sh
kubectl describe vpa my-app-vpa
```

Если всё работает как надо, Kubernetes теперь будет **автоматически** управлять нагрузкой. 🔥

Дай знать, если будут вопросы! 😊

