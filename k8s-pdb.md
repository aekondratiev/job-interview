Kubernetes Pod Disruption Budget (PDB) — это механизм, который позволяет контролировать количество подов (pods) определенного приложения, которые могут быть одновременно удалены или остановлены в результате добровольных нарушений (voluntary disruptions). PDB помогает обеспечить высокую доступность приложения, ограничивая количество подов, которые могут быть выведены из строя во время плановых операций, таких как обновления узлов, масштабирование кластера или ручное удаление подов.

### Основные понятия:
1. **Добровольные нарушения (Voluntary Disruptions)**:
   - Это ситуации, когда поды удаляются или останавливаются в результате действий, инициированных администратором или системой, например:
     - Обновление узлов (drain).
     - Удаление подов вручную.
     - Масштабирование Deployment или StatefulSet.

2. **Недобровольные нарушения (Involuntary Disruptions)**:
   - Это ситуации, когда поды прекращают работу из-за сбоев, например:
     - Сбой узла.
     - Нехватка ресурсов на узле.
     - Аппаратные сбои.
   - PDB не влияет на недобровольные нарушения.

### Как работает PDB:
PDB задает минимальное количество подов или процент от общего числа подов, которые должны оставаться доступными во время добровольных нарушений. Например:
- Если у вас есть Deployment с 10 подами, и вы задаете PDB, чтобы минимум 80% подов оставались доступными, то Kubernetes не позволит удалить более 2 подов одновременно.

### Пример PDB:
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 80%  # Минимум 80% подов должны оставаться доступными
  selector:
    matchLabels:
      app: my-app
```

Или можно указать абсолютное количество подов:
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  minAvailable: 5  # Минимум 5 подов должны оставаться доступными
  selector:
    matchLabels:
      app: my-app
```

Также можно использовать `maxUnavailable` вместо `minAvailable`:
```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: my-app-pdb
spec:
  maxUnavailable: 1  # Максимум 1 под может быть недоступен
  selector:
    matchLabels:
      app: my-app
```

### Как PDB применяется:
1. **При обновлении узлов**:
   - Если вы выполняете операцию `kubectl drain` для узла, Kubernetes проверит PDB для всех подов на этом узле.
   - Если удаление подов нарушит PDB, операция `drain` будет заблокирована до тех пор, пока нарушение не будет разрешено.

2. **При масштабировании Deployment или StatefulSet**:
   - Если масштабирование приведет к нарушению PDB, Kubernetes не позволит удалить поды.

3. **При ручном удалении подов**:
   - Если удаление пода нарушит PDB, операция будет заблокирована.

### Преимущества PDB:
- **Обеспечение доступности**: PDB помогает гарантировать, что критически важные приложения останутся доступными во время плановых операций.
- **Гибкость**: Можно настроить PDB как в абсолютных значениях (количество подов), так и в процентах.
- **Интеграция с другими механизмами**: PDB работает совместно с другими функциями Kubernetes, такими как Deployment, StatefulSet и обновления узлов.

### Ограничения:
- **Только для добровольных нарушений**: PDB не защищает от недобровольных нарушений, таких как сбои узлов.
- **Требует настройки**: Неправильная настройка PDB может привести к блокировке важных операций, таких как обновления или масштабирование.

### Пример использования:
1. Создайте Deployment:
   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: my-app
   spec:
     replicas: 10
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
           image: nginx
   ```

2. Создайте PDB для этого Deployment:
   ```yaml
   apiVersion: policy/v1
   kind: PodDisruptionBudget
   metadata:
     name: my-app-pdb
   spec:
     minAvailable: 8  # Минимум 8 подов должны оставаться доступными
     selector:
       matchLabels:
         app: my-app
   ```

3. Примените манифесты:
   ```bash
   kubectl apply -f deployment.yaml
   kubectl apply -f pdb.yaml
   ```

Теперь, если вы попытаетесь удалить более 2 подов одновременно (например, с помощью `kubectl drain`), Kubernetes заблокирует операцию, чтобы не нарушить PDB.

### Заключение:
Pod Disruption Budget — это важный инструмент для обеспечения высокой доступности приложений в Kubernetes. Он позволяет контролировать, сколько подов могут быть удалены во время плановых операций, что особенно полезно для критически важных приложений. Однако PDB требует аккуратной настройки и понимания его ограничений.

