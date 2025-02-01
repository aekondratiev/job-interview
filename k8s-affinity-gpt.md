Конечно, вот шпаргалка по **Node Affinity** и **Anti-Affinity** в Kubernetes в формате Markdown:

```markdown
# Kubernetes Node Affinity и Anti-Affinity Cheat Sheet

## 1. Что такое Node Affinity и Anti-Affinity?

- **Node Affinity**: Механизм, позволяющий ограничить размещение подов на узлах кластера на основе меток (labels) узлов.

- **Node Anti-Affinity**: В Kubernetes нет прямого механизма Node Anti-Affinity. Однако схожее поведение можно достичь, используя операторы `NotIn` или `DoesNotExist` в Node Affinity.

## 2. Типы Node Affinity

- **`requiredDuringSchedulingIgnoredDuringExecution`**: Жёсткое требование. Под будет запущен только на узле, соответствующем указанным условиям. Если подходящего узла нет, под не будет запущен.

- **`preferredDuringSchedulingIgnoredDuringExecution`**: Мягкое предпочтение. Планировщик постарается разместить под на узле, соответствующем условиям, но если такого узла нет, под всё равно будет запущен на другом узле.

## 3. Синтаксис Node Affinity

Node Affinity задаётся в спецификации пода в разделе `affinity.nodeAffinity`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: example-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: <node-label-key>
            operator: In
            values:
            - <node-label-value>
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: <weight>
        preference:
          matchExpressions:
          - key: <node-label-key>
            operator: In
            values:
            - <node-label-value>
  containers:
  - name: example-container
    image: <image-name>
```

**Параметры:**

- **`nodeSelectorTerms`**: Список условий для выбора узлов.

- **`matchExpressions`**: Список выражений для сопоставления меток узлов.

- **`operator`**: Оператор сравнения (`In`, `NotIn`, `Exists`, `DoesNotExist`).

- **`values`**: Список значений для сравнения.

- **`weight`**: Вес предпочтения (от 1 до 100).

## 4. Примеры использования

### **4.1. Жёсткое требование (requiredDuringSchedulingIgnoredDuringExecution)**

Разместить под только на узлах с меткой `disktype=ssd`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: ssd-only-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
  containers:
  - name: example-container
    image: nginx
```

### **4.2. Мягкое предпочтение (preferredDuringSchedulingIgnoredDuringExecution)**

Предпочесть узлы с меткой `zone=us-east1`, но при отсутствии таких узлов запустить под на любом доступном:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: preferred-zone-pod
spec:
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 50
        preference:
          matchExpressions:
          - key: zone
            operator: In
            values:
            - us-east1
  containers:
  - name: example-container
    image: nginx
```

### **4.3. Исключение узлов (эмуляция Node Anti-Affinity)**

Избежать размещения пода на узлах с меткой `disktype=hdd`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: avoid-hdd-pod
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: NotIn
            values:
            - hdd
  containers:
  - name: example-container
    image: nginx
```

## 5. Вопросы для собеседования

1. **Что такое Node Affinity в Kubernetes?**

   Node Affinity — это механизм, позволяющий ограничить размещение подов на узлах на основе меток узлов.

2. **Какие существуют типы Node Affinity?**

   Существуют два типа: `requiredDuringSchedulingIgnoredDuringExecution` и `preferredDuringSchedulingIgnoredDuringExecution`.

3. **Как эмулировать поведение Node Anti-Affinity?**

   Используя оператор `NotIn` или `DoesNotExist` в Node Affinity, можно избежать размещения подов на определённых узлах.

4. **В чём разница между `nodeSelector` и Node Affinity?**

   `nodeSelector` предоставляет базовый способ ограничения размещения подов на узлах с определёнными метками, тогда как Node Affinity предлагает более гибкий и мощный синтаксис для определения условий размещения, включая возможность указания мягких предпочтений.

5. **Что означает `IgnoredDuringExecution` в контексте Node Affinity?**

   Это означает, что если метки узла изменятся после размещения пода, под продолжит работать на этом узле, несмотря на несоответствие новым условиям.

---

