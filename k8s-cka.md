Информация на тему **Certified Kubernetes Administrator (CKA)**. Она охватывает ключевые темы, команды и концепции, которые важно знать для успешного прохождения собеседования.

---

## **1. Основные концепции Kubernetes**

### **Архитектура Kubernetes**
- **Master Node**:
  - **API Server**: Точка входа для управления кластером.
  - **Scheduler**: Распределяет поды по узлам.
  - **Controller Manager**: Управляет контроллерами (ReplicaSet, Deployment и т.д.).
  - **etcd**: Хранилище ключ-значение для данных кластера.
- **Worker Node**:
  - **Kubelet**: Агент, управляющий подами на узле.
  - **Kube Proxy**: Обеспечивает сетевое взаимодействие.
  - **Container Runtime**: Docker, containerd и т.д.

### **Основные объекты Kubernetes**
- **Pod**: Наименьшая единица развертывания (один или несколько контейнеров).
- **Deployment**: Управляет репликами подов и обновлениями.
- **Service**: Обеспечивает доступ к подам (ClusterIP, NodePort, LoadBalancer).
- **ConfigMap/Secret**: Хранение конфигураций и секретов.
- **Namespace**: Логическое разделение ресурсов в кластере.

---

## **2. Основные команды kubectl**

### **Управление ресурсами**
- Создание ресурса:
  ```bash
  kubectl create -f file.yaml
  ```
- Применение изменений:
  ```bash
  kubectl apply -f file.yaml
  ```
- Просмотр ресурсов:
  ```bash
  kubectl get pods
  kubectl get services
  kubectl get deployments
  ```
- Описание ресурса:
  ```bash
  kubectl describe pod <pod-name>
  ```
- Удаление ресурса:
  ```bash
  kubectl delete pod <pod-name>
  ```

### **Логи и отладка**
- Просмотр логов пода:
  ```bash
  kubectl logs <pod-name>
  ```
- Выполнение команды в контейнере:
  ```bash
  kubectl exec -it <pod-name> -- /bin/sh
  ```

### **Масштабирование и обновления**
- Масштабирование Deployment:
  ```bash
  kubectl scale deployment <deployment-name> --replicas=3
  ```
- Обновление образа в Deployment:
  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```

---

## **3. Сетевые настройки**

### **Service**
- **ClusterIP**: Внутренний IP для доступа внутри кластера.
- **NodePort**: Открывает порт на всех узлах.
- **LoadBalancer**: Интеграция с облачными провайдерами для балансировки нагрузки.

### **Ingress**
- Управление входящим трафиком:
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example-ingress
  spec:
    rules:
    - host: example.com
      http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: example-service
              port:
                number: 80
  ```

---

## **4. Хранение данных**

### **Persistent Volume (PV) и Persistent Volume Claim (PVC)**
- **PV**: Ресурс хранения в кластере.
- **PVC**: Запрос на выделение PV.

Пример:
```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /mnt/data
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

---

## **5. Безопасность**

### **RBAC (Role-Based Access Control)**
- **Role/ClusterRole**: Набор разрешений.
- **RoleBinding/ClusterRoleBinding**: Связь между ролью и пользователем/группой.

Пример:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

### **Security Context**
- Настройка прав доступа для контейнеров:
  ```yaml
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  ```

---

## **6. Мониторинг и логирование**

### **Metrics Server**
- Установка:
  ```bash
  kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
  ```
- Просмотр метрик:
  ```bash
  kubectl top nodes
  kubectl top pods
  ```

### **Liveness и Readiness Probes**
- **Liveness Probe**: Проверка, что контейнер работает.
- **Readiness Probe**: Проверка, что контейнер готов принимать трафик.

Пример:
```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 3
  periodSeconds: 3
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

---

## **7. Вопросы на собеседовании**

1. **Как настроить Deployment с 3 репликами?**
   - Создать Deployment с `replicas: 3`.

2. **Как обеспечить доступ к приложению извне?**
   - Использовать Service типа LoadBalancer или Ingress.

3. **Как настроить Persistent Volume?**
   - Создать PV и PVC, подключить к поду.

4. **Как настроить RBAC для пользователя?**
   - Создать Role и RoleBinding.

5. **Как отладить проблему с подом?**
   - Использовать `kubectl describe pod`, `kubectl logs`, `kubectl exec`.

---

