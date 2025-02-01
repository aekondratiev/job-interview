# Kubernetes Selector Cheat Sheet

## 1. Что такое Selector в Kubernetes?
Selector — это механизм в Kubernetes, позволяющий находить объекты на основе меток (labels) или аннотаций.

## 2. Где используется Selector?
- **Pods** (например, в Service, Deployment, ReplicaSet)
- **NodeSelector** (для назначения подов на определённые узлы)
- **PersistentVolumeClaim (PVC)** (для выбора подходящего PersistentVolume)
- **NetworkPolicy** (для фильтрации трафика между подами)

## 3. Виды Selector

### Label Selector (по меткам)
Использует метки (labels) для фильтрации объектов.

#### **Простой (Exact Match)**
```yaml
selector:
  matchLabels:
    app: myapp
```
Найдёт все объекты с меткой **app=myapp**.

#### **Выражения (matchExpressions)**
Позволяют использовать логические операторы.
```yaml
selector:
  matchExpressions:
    - key: env
      operator: In
      values: ["staging", "production"]
    - key: tier
      operator: NotIn
      values: ["frontend"]
```
Найдёт все объекты, у которых:
- ✅ `env` = `staging` или `production`
- ❌ `tier` ≠ `frontend`

##### **Операторы:**
- `In` — значение в списке
- `NotIn` — значение не в списке
- `Exists` — ключ существует
- `DoesNotExist` — ключ отсутствует

## 4. Примеры использования Selector в Kubernetes

### **Selector в Service**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```
Этот Service будет направлять трафик на поды с `app=myapp`.

### **Selector в Deployment**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: my-container
        image: nginx
```
Этот Deployment управляет только подами с `app=myapp`.

### **NodeSelector в Pod**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  nodeSelector:
    disktype: ssd
  containers:
  - name: my-container
    image: nginx
```
Под будет запущен только на ноде с меткой `disktype=ssd`.

### **PVC Selector для PersistentVolume**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-pvc
spec:
  selector:
    matchLabels:
      storage-tier: fast
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```
Найдёт `PersistentVolume` с `storage-tier=fast`.

## 5. Вопросы на собеседовании

### **Базовые вопросы:**
1. Что такое Label Selector в Kubernetes?
2. Где можно использовать Selector?
3. Чем отличается `matchLabels` от `matchExpressions`?

### **Продвинутые вопросы:**
4. Можно ли изменить selector у уже созданного Deployment?
   - ❌ Нет, его нельзя изменить после создания. Нужно пересоздать объект.
5. В чём разница между `matchExpressions` и `nodeSelector`?
   - `matchExpressions` используется в Label Selector, а `nodeSelector` — для выбора ноды.
6. Как выбрать поды, у которых есть ключ `env`, но его значение неизвестно?
   - Использовать `Exists`:
   ```yaml
   selector:
     matchExpressions:
       - key: env
         operator: Exists
   ```

