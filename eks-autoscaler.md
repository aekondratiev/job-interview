Информация по **EKS Autoscaling**. Она охватывает ключевые концепции, компоненты и настройки, связанные с автоматическим масштабированием в Amazon Elastic Kubernetes Service (EKS).

---

## **1. Основные концепции**

### **EKS (Elastic Kubernetes Service)**
- Управляемый сервис Kubernetes от AWS.
- Позволяет развертывать, управлять и масштабировать контейнерные приложения.

### **Autoscaling в EKS**
- Масштабирование кластера EKS может происходить на двух уровнях:
  1. **Масштабирование подов (Pods)** — с помощью Horizontal Pod Autoscaler (HPA).
  2. **Масштабирование узлов (Nodes)** — с помощью Cluster Autoscaler (CA).

---

## **2. Horizontal Pod Autoscaler (HPA)**

### **Что делает HPA?**
- Автоматически увеличивает или уменьшает количество подов в зависимости от нагрузки (например, CPU, memory или custom metrics).

### **Как работает HPA?**
1. HPA отслеживает метрики (CPU, memory или кастомные метрики) через Metrics Server.
2. Если нагрузка превышает заданный порог, HPA увеличивает количество подов.
3. Если нагрузка ниже порога, HPA уменьшает количество подов.

### **Пример настройки HPA:**
```yaml
apiVersion: autoscaling/v2beta2
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

### **Ключевые параметры HPA:**
- `minReplicas`: Минимальное количество подов.
- `maxReplicas`: Максимальное количество подов.
- `metrics`: Метрики для масштабирования (CPU, memory, custom metrics).

---

## **3. Cluster Autoscaler (CA)**

### **Что делает Cluster Autoscaler?**
- Автоматически увеличивает или уменьшает количество узлов (worker nodes) в кластере EKS в зависимости от потребностей подов.

### **Как работает CA?**
1. CA отслеживает поды, которые не могут быть запущены из-за нехватки ресурсов (pending pods).
2. Если есть pending pods, CA добавляет новые узлы.
3. Если узлы недогружены, CA удаляет их.

### **Требования для CA:**
- Worker nodes должны быть частью Auto Scaling Group (ASG).
- ASG должна быть настроена с минимальным и максимальным количеством узлов.

### **Пример настройки CA:**
1. Установите Cluster Autoscaler:
   ```bash
   kubectl apply -f https://github.com/kubernetes/autoscaler/releases/download/cluster-autoscaler-<version>/cluster-autoscaler-autodiscover.yaml
   ```
2. Настройте ASG:
   - Убедитесь, что ASG имеет тег `k8s.io/cluster-autoscaler/enabled=true`.
   - Укажите минимальное и максимальное количество узлов.

### **Ключевые параметры CA:**
- `--scale-down-enabled`: Включение/отключение уменьшения узлов.
- `--scale-down-delay-after-add`: Задержка перед уменьшением после добавления узлов.
- `--scale-down-unneeded-time`: Время, через которое узел считается недогруженным.

---

## **4. Vertical Pod Autoscaler (VPA)**

### **Что делает VPA?**
- Автоматически настраивает запросы и лимиты ресурсов (CPU, memory) для подов.

### **Как работает VPA?**
1. VPA анализирует историческое использование ресурсов подами.
2. На основе анализа VPA предлагает новые значения для `requests` и `limits`.
3. VPA может автоматически применять изменения (в режиме `Auto`).

### **Пример настройки VPA:**
```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: "apps/v1"
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
```

### **Режимы VPA:**
- `Off`: Только рекомендации.
- `Initial`: Применяет рекомендации при создании пода.
- `Auto`: Автоматически применяет рекомендации.

---

## **5. Полезные команды**

### **Проверка состояния HPA:**
```bash
kubectl get hpa
```

### **Просмотр событий Cluster Autoscaler:**
```bash
kubectl logs -n kube-system deployment/cluster-autoscaler
```

### **Проверка pending pods:**
```bash
kubectl get pods --field-selector=status.phase=Pending
```

### **Просмотр метрик (требуется Metrics Server):**
```bash
kubectl top pods
kubectl top nodes
```

---

## **6. Best Practices**

1. **Используйте HPA и CA вместе**:
   - HPA масштабирует поды, а CA масштабирует узлы.
   - Это обеспечивает полное автоматическое масштабирование.

2. **Настройте правильные запросы и лимиты**:
   - Убедитесь, что у подов заданы `requests` и `limits` для CPU и memory.
   - Это помогает HPA и CA принимать правильные решения.

3. **Используйте Spot Instances для экономии**:
   - Настройте ASG для использования Spot Instances, чтобы снизить стоимость.

4. **Мониторинг и логирование**:
   - Используйте CloudWatch для мониторинга метрик и логов.
   - Настройте уведомления о масштабировании.

---

## **7. Пример архитектуры**

1. **HPA**:
   - Отслеживает метрики подов.
   - Увеличивает/уменьшает количество подов.

2. **Cluster Autoscaler**:
   - Отслеживает pending pods.
   - Увеличивает/уменьшает количество узлов через ASG.

3. **VPA**:
   - Оптимизирует запросы и лимиты ресурсов для подов.

---

## **8. Вопросы на собеседовании**

1. **Как работает HPA?**
   - HPA отслеживает метрики (CPU, memory) и масштабирует поды на основе заданных порогов.

2. **Как настроить Cluster Autoscaler в EKS?**
   - Установите CA, настройте ASG с тегами и убедитесь, что узлы могут автоматически добавляться/удаляться.

3. **В чем разница между HPA и VPA?**
   - HPA масштабирует количество подов, а VPA настраивает запросы и лимиты ресурсов для подов.

4. **Как уменьшить стоимость кластера EKS?**
   - Используйте Spot Instances, настройте правильные запросы и лимиты, оптимизируйте использование ресурсов.

---

