Ключевые концепции, команды и компоненты Kubernetes.

---

## **Основные концепции Kubernetes**

### 1. **Кластер Kubernetes**
- **Control Plane (Master)**: Управляет кластером. Включает:
  - **API Server**: Основной интерфейс для управления кластером.
  - **Scheduler**: Распределяет поды по узлам.
  - **Controller Manager**: Управляет контроллерами (например, ReplicaSet, Deployment).
  - **etcd**: Хранилище ключ-значение для данных кластера.
- **Worker Nodes**: Узлы, на которых запускаются поды. Включают:
  - **Kubelet**: Агент, управляющий подами на узле.
  - **Kube-proxy**: Обеспечивает сетевое взаимодействие.
  - **Container Runtime**: Например, Docker или containerd.

---

### 2. **Основные объекты Kubernetes**
- **Pod**: Минимальная единица развертывания. Может содержать один или несколько контейнеров.
- **Deployment**: Управляет репликами подов и обеспечивает обновления и откаты.
- **Service**: Обеспечивает стабильный доступ к подам (например, через LoadBalancer или ClusterIP).
- **ConfigMap**: Хранит конфигурационные данные.
- **Secret**: Хранит чувствительные данные (например, пароли, токены).
- **Namespace**: Логическое разделение ресурсов в кластере.
- **ReplicaSet**: Обеспечивает запуск указанного количества реплик подов.
- **StatefulSet**: Управляет stateful-приложениями (например, базами данных).
- **DaemonSet**: Обеспечивает запуск пода на каждом узле.
- **Job/CronJob**: Запускает задачи, которые должны завершиться (однократно или по расписанию).

---

### 3. **Сетевые концепции**
- **ClusterIP**: Внутренний IP-адрес для доступа к сервису внутри кластера.
- **NodePort**: Открывает порт на каждом узле для доступа к сервису.
- **LoadBalancer**: Интегрируется с облачным провайдером для создания внешнего балансировщика нагрузки.
- **Ingress**: Управляет внешним доступом к сервисам через HTTP/HTTPS.

---

### 4. **Хранение данных**
- **PersistentVolume (PV)**: Ресурс хранения в кластере.
- **PersistentVolumeClaim (PVC)**: Запрос на выделение PV.
- **StorageClass**: Описывает тип хранилища (например, SSD, HDD).

---

## **Команды `kubectl`**

### 1. **Основные команды**
- Просмотр ресурсов:
  ```bash
  kubectl get pods
  kubectl get services
  kubectl get deployments
  ```
- Подробная информация о ресурсе:
  ```bash
  kubectl describe pod <pod-name>
  ```
- Создание ресурса:
  ```bash
  kubectl apply -f <file.yaml>
  ```
- Удаление ресурса:
  ```bash
  kubectl delete pod <pod-name>
  ```

### 2. **Логи и отладка**
- Просмотр логов пода:
  ```bash
  kubectl logs <pod-name>
  ```
- Выполнение команды в контейнере:
  ```bash
  kubectl exec -it <pod-name> -- /bin/sh
  ```

### 3. **Масштабирование и обновления**
- Масштабирование Deployment:
  ```bash
  kubectl scale deployment <deployment-name> --replicas=3
  ```
- Обновление образа в Deployment:
  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```
- Откат Deployment:
  ```bash
  kubectl rollout undo deployment/<deployment-name>
  ```

---

## **Примеры манифестов**

### 1. **Pod**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: my-container
    image: nginx
```

### 2. **Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
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
      - name: my-container
        image: nginx
```

### 3. **Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: my-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: LoadBalancer
```

---

## **Часто задаваемые вопросы на собеседовании**

### 1. **Чем отличается Pod от Container?**
- Pod — это минимальная единица развертывания в Kubernetes, которая может содержать один или несколько контейнеров. Контейнеры внутри одного пода разделяют сетевые и storage-ресурсы.

### 2. **Как работает Deployment?**
- Deployment управляет ReplicaSet, который, в свою очередь, управляет подами. Deployment обеспечивает обновления, откаты и масштабирование.

### 3. **Что такое ConfigMap и Secret?**
- ConfigMap хранит конфигурационные данные в виде пар ключ-значение.
- Secret хранит чувствительные данные (например, пароли, токены) в зашифрованном виде.

### 4. **Как работает Ingress?**
- Ingress управляет внешним доступом к сервисам через HTTP/HTTPS. Он использует правила для маршрутизации трафика на соответствующие сервисы.

### 5. **Как обеспечить отказоустойчивость в Kubernetes?**
- Использование ReplicaSet/Deployment для запуска нескольких реплик подов.
- Настройка liveness и readiness проб для мониторинга состояния приложения.
- Использование PersistentVolume для хранения данных.

---

## **Полезные ссылки**
- Официальная документация Kubernetes: [https://kubernetes.io/docs/](https://kubernetes.io/docs/)
- Интерактивный туториал: [https://kubernetes.io/docs/tutorials/](https://kubernetes.io/docs/tutorials/)
- Шпаргалка по `kubectl`: [https://kubernetes.io/docs/reference/kubectl/cheatsheet/](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

Эта шпаргалка охватывает основные темы, которые могут быть затронуты на собеседовании. Удачи! 🚀
