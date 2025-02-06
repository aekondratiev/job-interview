### **Pod Topology Spread Constraints в Kubernetes**  

**Pod Topology Spread Constraints** – это механизм в Kubernetes, который позволяет равномерно распределять поды по кластерам, чтобы улучшить отказоустойчивость и балансировку нагрузки.  

#### 🔹 **Зачем это нужно?**  
- Избегание концентрации подов на одних и тех же узлах.  
- Обеспечение высокой доступности (HA) при сбоях узлов или зон.  
- Балансировка нагрузки между разными топологическими доменами (узлы, зоны, регионы и т. д.).  

---

## **Как работает?**  

Настраивается в манифесте пода с помощью поля `topologySpreadConstraints`. Основные параметры:  

- **`topologyKey`** – ключ аннотации, по которому определяются топологические домены (например, `topology.kubernetes.io/zone` для зон).  
- **`whenUnsatisfiable`** – стратегия, если распределение невозможно (`ScheduleAnyway` или `DoNotSchedule`).  
- **`maxSkew`** – максимальная разница между количеством подов в разных доменах.  
- **`labelSelector`** – выбирает поды, к которым применяется правило.  

---

## **Пример манифеста**  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 6
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: topology.kubernetes.io/zone
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: my-app
      containers:
        - name: my-app
          image: my-app:latest
```

---

## **Разбор параметров в примере:**  
- Поддерживается баланс между зонами (`topology.kubernetes.io/zone`).  
- Разница (`maxSkew`) между наименее и наиболее загруженными зонами не превышает 1.  
- Если равномерное распределение невозможно, под не будет запланирован (`DoNotSchedule`).  

### **Дополнительные ключи для `topologyKey`:**  
- `kubernetes.io/hostname` – распределение между нодами.  
- `topology.kubernetes.io/zone` – между зонами.  
- `topology.kubernetes.io/region` – между регионами.  

---

## **Когда использовать?**  
✅ Если нужно равномерно распределять поды между нодами или зонами.  
✅ Когда важна отказоустойчивость (например, при падении одной зоны).  
✅ При необходимости ограничения плотности подов на одной ноде/зоне.  

🚀 **Вывод:** `Pod Topology Spread Constraints` – это мощный инструмент, который помогает балансировать поды в кластере для лучшей отказоустойчивости и высокой доступности.

