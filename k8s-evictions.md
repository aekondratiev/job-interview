### 📌 Когда в Kubernetes происходит **eviction (выселение) подов?**  

**Eviction** (выселение) — это процесс, при котором **Kubernetes удаляет поды** с узлов по разным причинам.  

---

## 🔥 Основные причины eviction подов  

### 1️⃣ **Taint с `NoExecute` (принудительное выселение)**  
- Если узел получает taint с **`NoExecute`**, все поды **без соответствующего toleration** будут немедленно удалены.  
- Если у пода есть `tolerationSeconds`, он останется на узле на указанное время, а затем будет удалён.  
- **Пример** добавления `NoExecute` taint:
  ```bash
  kubectl taint nodes my-node key=value:NoExecute
  ```
  🔹 Все неподходящие поды будут выселены.  

---

### 2️⃣ **Node Pressure Eviction (недостаток ресурсов: CPU, RAM, Disk)**  
Kubernetes **автоматически удаляет поды**, если на узле заканчиваются **память (MemoryPressure), место на диске (DiskPressure) или inodes (NodePressure)**.  

- **Kubelet** мониторит состояние узла и выселяет поды, начиная с **наименее приоритетных**.  
- **Пример логов выселения:**
  ```bash
  kubectl get events --sort-by=.metadata.creationTimestamp
  ```
  🔹 Можно увидеть события типа `Evicted`.  

---

### 3️⃣ **Preemption (выселение подов при дефиците ресурсов)**  
Если на узле **не хватает ресурсов** для запуска пода с **высоким приоритетом (`priorityClass`)**, Kubernetes **может удалить менее приоритетные поды**, чтобы освободить место.  

- **Пример:**  
  - **Поды с `priorityClass: high-priority`** могут вытеснить поды с `priorityClass: low-priority`.  

- **Как посмотреть приоритеты подов?**
  ```bash
  kubectl get pods -o=jsonpath='{range .items[*]}{.metadata.name}{" "}{.spec.priorityClassName}{"\n"}{end}'
  ```

---

### 4️⃣ **Pod Disruption Budget (PDB) + Voluntary Eviction**  
Kubernetes **может выселять поды** при обновлениях или удалении узлов, но соблюдает **PodDisruptionBudget (PDB)**, который ограничивает **максимальное количество подов**, которое можно удалить одновременно.  

- **Пример PDB:**  
  ```yaml
  apiVersion: policy/v1
  kind: PodDisruptionBudget
  metadata:
    name: my-app-pdb
  spec:
    minAvailable: 2  # Минимум 2 пода должны оставаться запущенными
    selector:
      matchLabels:
        app: my-app
  ```
  🔹 Kubernetes не удалит больше подов, чем разрешено PDB.  

---

### 5️⃣ **`kubectl drain` (ручное выселение подов)**  
- Когда администратор хочет удалить узел или подготовить его к обновлению, он может выполнить:
  ```bash
  kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
  ```
  🔹 Все поды будут выселены, кроме `DaemonSet`.  

---

### 6️⃣ **Eviction через Kubernetes Descheduler**  
- `Descheduler` может **удалять поды**, если они больше не соответствуют `nodeAffinity`, `taints`, или узел перегружен.  
- **Пример стратегии в Descheduler:**
  ```yaml
  strategies:
    RemovePodsViolatingNodeAffinity:
      enabled: true
  ```
  🔹 Это удалит поды, если `nodeAffinity` изменился.  

---

## ✅ Итог  
**Поды выселяются в следующих случаях:**  
1️⃣ **Taint `NoExecute`** — под удаляется, если у него нет toleration.  
2️⃣ **Недостаток ресурсов (MemoryPressure, DiskPressure)** — kubelet автоматически выселяет поды.  
3️⃣ **Preemption** — если приходит под с **более высоким приоритетом**.  
4️⃣ **Pod Disruption Budget (PDB) + Voluntary Eviction** — контролирует, сколько подов можно удалить.  
5️⃣ **`kubectl drain`** — администратор вручную выселяет поды.  
6️⃣ **Descheduler** — удаляет поды, которые больше не соответствуют условиям.  



