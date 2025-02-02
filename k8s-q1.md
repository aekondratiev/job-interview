Вот список вопросов и ответов в формате **Markdown** для собеседования на **EKS DevOps Engineer**.  

---

# 🛠 Вопросы для собеседования **EKS DevOps Engineer**

## 1. Основы Kubernetes и EKS  

### **Вопрос:** Что такое Amazon EKS?  
**Ответ:**  
Amazon **EKS (Elastic Kubernetes Service)** — это управляемый сервис Kubernetes в AWS. Он упрощает развертывание, управление и масштабирование контейнерных приложений, устраняя необходимость администрирования control plane.  

### **Вопрос:** В чем разница между EKS, ECS и Fargate?  
**Ответ:**  
- **EKS (Elastic Kubernetes Service)** – управляемый Kubernetes в AWS  
- **ECS (Elastic Container Service)** – сервис для управления контейнерами, но без Kubernetes  
- **Fargate** – безсерверный вариант для ECS и EKS, не требующий управления EC2  

---

## 2. Развертывание и управление EKS-кластером  

### **Вопрос:** Как создать кластер EKS?  
**Ответ:**  
1. **Создать VPC** с нужными подсетями и настройками  
2. **Создать IAM-роль** для EKS  
3. **Развернуть кластер** через AWS Console, AWS CLI или Terraform  
4. **Добавить worker-ноды** (EC2 или Fargate)  
5. **Настроить kubectl** для работы с кластером  

### **Вопрос:** Как настроить kubectl для работы с EKS?  
```sh
aws eks update-kubeconfig --name <cluster-name> --region <region>
```
Эта команда обновляет `~/.kube/config`, добавляя в него контекст для кластера EKS.  

---

## 3. Управление узлами (Worker Nodes)  

### **Вопрос:** Какие типы нод можно использовать в EKS?  
**Ответ:**  
- **EC2-based nodes** – управляемые вручную или через Auto Scaling  
- **Fargate nodes** – безсерверные, управляемые AWS  

### **Вопрос:** Как добавить worker-ноды в EKS?  
**Ответ:**  
- Через **Managed Node Groups** (управляемые AWS)  
- Через **Self-Managed Nodes** (настраиваются вручную с EC2)  

---

## 4. Сетевое взаимодействие и безопасность  

### **Вопрос:** Как работает сеть в EKS?  
**Ответ:**  
EKS использует **Amazon VPC CNI** (Container Network Interface), который позволяет каждому поду получать IP-адрес из VPC.  

### **Вопрос:** Как организовать доступ к EKS из внешнего мира?  
**Ответ:**  
Можно использовать:  
- **AWS Load Balancer Controller** (ALB/NLB)  
- **Ingress-контроллер** (например, Nginx Ingress)  
- **Service type LoadBalancer**  

### **Вопрос:** Как ограничить доступ к EKS?  
**Ответ:**  
- Использовать **IAM-политику** для контроля доступа  
- Настроить **RBAC** в Kubernetes  
- Ограничить доступ через **Security Groups и Network Policies**  

---

## 5. Мониторинг и логирование  

### **Вопрос:** Какие инструменты AWS используются для мониторинга EKS?  
**Ответ:**  
- **Amazon CloudWatch** – сбор метрик  
- **AWS Managed Prometheus** – мониторинг через Prometheus  
- **AWS Managed Grafana** – визуализация метрик  
- **Fluent Bit / Fluentd** – сбор логов в CloudWatch  

### **Вопрос:** Как настроить логирование подов в EKS?  
**Ответ:**  
Можно настроить **Fluent Bit** для отправки логов в **CloudWatch**, **Elasticsearch** или **S3**.  

---

## 6. CI/CD для EKS  

### **Вопрос:** Как развертывать приложения в EKS через CI/CD?  
**Ответ:**  
Обычно используется следующая схема:  
1. **Хранение кода** в CodeCommit/GitHub/GitLab  
2. **Сборка образа** в CodeBuild или Jenkins  
3. **Загрузка образа** в Amazon ECR  
4. **Развертывание** через ArgoCD/GitOps или CodePipeline  

### **Вопрос:** Какие инструменты используются для GitOps в EKS?  
**Ответ:**  
- **ArgoCD** – популярный инструмент GitOps  
- **FluxCD** – альтернатива ArgoCD  

---

## 7. Масштабирование и High Availability  

### **Вопрос:** Как работает масштабирование в EKS?  
**Ответ:**  
EKS поддерживает три типа масштабирования:  
1. **Horizontal Pod Autoscaler (HPA)** – автоматически увеличивает/уменьшает количество подов  
2. **Cluster Autoscaler** – добавляет или удаляет worker-ноды  
3. **Karpenter** – современный инструмент для авто-скейлинга в AWS  

### **Вопрос:** Как настроить HPA?  
```sh
kubectl autoscale deployment my-app --cpu-percent=50 --min=2 --max=10
```
Эта команда автоматически масштабирует поды при нагрузке на CPU > 50%.  

---

## 8. Безопасность и IAM  

### **Вопрос:** Как настроить IAM-роли для подов в EKS?  
**Ответ:**  
Можно использовать **IAM Roles for Service Accounts (IRSA)**, что позволяет назначать IAM-политику отдельным подам.  

### **Вопрос:** Как управлять доступом к EKS?  
**Ответ:**  
- IAM для управления доступом к кластеру  
- RBAC (Role-Based Access Control) в Kubernetes  
- Network Policies для ограничения сетевого трафика  

---

## 9. Обновление кластера EKS  

### **Вопрос:** Как обновить версию Kubernetes в EKS?  
**Ответ:**  
1. Обновить **control plane** через AWS Console или CLI  
2. Обновить **worker-ноды** (Managed Node Groups или вручную)  
3. Обновить **kubectl и Helm**  
4. Обновить **приложения** и CRD  

### **Вопрос:** Как проверить текущую версию кластера?  
```sh
kubectl version --short
aws eks describe-cluster --name my-cluster --query cluster.version
```

---

## 10. Хранение данных в EKS  

### **Вопрос:** Как организовать хранение данных для подов в EKS?  
**Ответ:**  
Можно использовать:  
- **Amazon EBS** – для StatefulSets  
- **Amazon EFS** – для шаринга данных между подами  
- **Amazon S3** – для объектов  

### **Вопрос:** Как создать Persistent Volume на основе EBS?  
```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: gp2
provisioner: ebs.csi.aws.com
```

---

Это основные вопросы для **EKS DevOps**. Если нужны более сложные кейсы или практические задания – могу добавить! 🚀



Вот список сложных кейсов для **EKS DevOps Engineer** в формате **Markdown**.  

---

# 🔥 Сложные кейсы для собеседования **EKS DevOps Engineer**

## 1️⃣ **Траблшутинг: Поды не могут подключиться к базе данных в EKS**  

### **Описание кейса:**  
Вы развернули приложение в **EKS**, но поды не могут подключиться к базе данных **RDS PostgreSQL**.  

### **Как расследовать проблему?**  
1. **Проверить, разрешены ли исходящие соединения у подов:**  
   ```sh
   kubectl exec -it <pod-name> -- curl -v <db-endpoint>:5432
   ```
2. **Проверить Security Groups у RDS и EKS:**  
   - Убедитесь, что RDS разрешает входящие соединения с подсетей EKS.  
   - Добавьте разрешение в **Security Group**:
     ```sh
     aws ec2 authorize-security-group-ingress \
       --group-id sg-xxxxx \
       --protocol tcp --port 5432 --source-group sg-yyyyy
     ```
3. **Проверить Network Policies в Kubernetes:**  
   - Если в кластере включены Network Policies, убедитесь, что есть разрешение на исходящий трафик:
     ```yaml
     apiVersion: networking.k8s.io/v1
     kind: NetworkPolicy
     metadata:
       name: allow-db-access
       namespace: my-app
     spec:
       podSelector:
         matchLabels:
           app: my-app
       policyTypes:
       - Egress
       egress:
       - to:
         - ipBlock:
             cidr: 10.0.0.0/16
         ports:
         - protocol: TCP
           port: 5432
     ```
4. **Проверить логин и пароль в секретах:**  
   ```sh
   kubectl get secrets my-db-secret -o yaml
   ```

---

## 2️⃣ **Авто-скейлинг кластера: Внезапная высокая нагрузка**  

### **Описание кейса:**  
Ваши поды испытывают высокую нагрузку, но новые инстансы не создаются.  

### **Как расследовать проблему?**  
1. **Проверить статус HPA:**  
   ```sh
   kubectl get hpa
   ```
   Если поды не масштабируются, проверьте метрики:  
   ```sh
   kubectl describe hpa my-app
   ```
2. **Проверить статус Cluster Autoscaler:**  
   ```sh
   kubectl get pods -n kube-system | grep cluster-autoscaler
   kubectl logs -f <cluster-autoscaler-pod> -n kube-system
   ```
3. **Проверить лимиты подов и узлов:**  
   ```sh
   kubectl describe node <node-name>
   ```
   Если узлы переполнены, увеличьте количество worker-нод в **Node Group**:
   ```sh
   aws eks update-nodegroup-config --cluster-name my-cluster \
     --nodegroup-name my-nodes --scaling-config minSize=2,maxSize=10,desiredSize=5
   ```
4. **Проверить Karpenter (если используется):**  
   ```sh
   kubectl get pods -n karpenter
   kubectl logs -f <karpenter-pod> -n karpenter
   ```

---

## 3️⃣ **Обновление кластера EKS с минимальным даунтаймом**  

### **Описание кейса:**  
Вам нужно обновить кластер EKS с **v1.24** до **v1.25** без остановки приложений.  

### **Как выполнить обновление?**  
1. **Проверить совместимость с новой версией:**  
   ```sh
   kubectl get apiservices | grep deprecated
   ```
2. **Обновить control plane:**  
   ```sh
   aws eks update-cluster-version --name my-cluster --kubernetes-version 1.25
   ```
3. **Обновить Managed Node Group:**  
   ```sh
   aws eks update-nodegroup-version --cluster-name my-cluster --nodegroup-name my-nodes
   ```
4. **Обновить kube-proxy, CoreDNS и VPC CNI:**  
   ```sh
   kubectl set image daemonset/kube-proxy -n kube-system \
     kube-proxy=602401143452.dkr.ecr.us-west-2.amazonaws.com/eks/kube-proxy:v1.25
   ```

---

## 4️⃣ **Развертывание приложения через GitOps (ArgoCD)**  

### **Описание кейса:**  
Ваша команда использует **ArgoCD** для развертывания приложений. Вам нужно настроить **GitOps** для автоматического деплоя.  

### **Как настроить?**  
1. **Установить ArgoCD:**  
   ```sh
   kubectl create namespace argocd
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```
2. **Создать `Application` манифест:**  
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: my-app
     namespace: argocd
   spec:
     destination:
       namespace: my-app
       server: https://kubernetes.default.svc
     source:
       repoURL: https://github.com/my-org/my-repo.git
       targetRevision: main
       path: k8s/manifests
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```
3. **Применить манифест:**  
   ```sh
   kubectl apply -f my-app-argo.yaml
   ```

---

## 5️⃣ **Инцидент: Поды перезапускаются с ошибкой OOMKilled**  

### **Описание кейса:**  
Приложение в Kubernetes постоянно перезапускается, и команда `kubectl describe pod` показывает статус `OOMKilled`.  

### **Как расследовать?**  
1. **Проверить логи:**  
   ```sh
   kubectl logs <pod-name>
   ```
2. **Посмотреть статус пода:**  
   ```sh
   kubectl get pod <pod-name> -o wide
   ```
3. **Проверить лимиты CPU и памяти:**  
   ```yaml
   resources:
     requests:
       memory: "512Mi"
       cpu: "250m"
     limits:
       memory: "1Gi"
       cpu: "500m"
   ```
4. **Проверить метрики в CloudWatch или Prometheus:**  
   ```sh
   kubectl top pod
   ```
5. **Решение проблемы:**  
   - Увеличить `limits.memory`  
   - Настроить **Horizontal Pod Autoscaler**  
   - Оптимизировать код приложения  

---

## 6️⃣ **Катастрофическое восстановление после сбоя EKS**  

### **Описание кейса:**  
Кластер EKS был случайно удален. Нужно восстановить его из бэкапа.  

### **Как выполнить восстановление?**  
1. **Проверить доступные бэкапы в Velero (если есть):**  
   ```sh
   velero backup get
   ```
2. **Развернуть новый кластер:**  
   ```sh
   eksctl create cluster --name my-cluster --region us-east-1
   ```
3. **Восстановить все ресурсы:**  
   ```sh
   velero restore create --from-backup my-backup
   ```
4. **Перенести Persistent Volumes:**  
   ```sh
   kubectl apply -f my-volumes.yaml
   ```
5. **Перенести ArgoCD или Helm-релизы:**  
   ```sh
   helm list --all-namespaces
   helm rollback <release-name> <revision>
   ```

---

Это сложные **EKS DevOps** кейсы, которые помогут подготовиться к реальным задачам. Если нужны дополнительные сценарии – спрашивай! 🚀



Вот еще несколько сложных кейсов для **EKS DevOps Engineer** в формате **Markdown**.  

---

# 🚀 Дополнительные сложные кейсы для **EKS DevOps Engineer**

## 7️⃣ **Приложение зависает из-за Deadlock в Kubernetes**  

### **Описание кейса:**  
Приложение в EKS зависает, и новые запросы не обрабатываются. Перезапуск подов временно решает проблему, но она возникает снова.  

### **Как расследовать проблему?**  
1. **Посмотреть состояние подов:**  
   ```sh
   kubectl get pods -n my-app
   ```
2. **Проверить логи:**  
   ```sh
   kubectl logs <pod-name> -n my-app
   ```
3. **Посмотреть статус контейнера:**  
   ```sh
   kubectl describe pod <pod-name> -n my-app
   ```
4. **Проверить блокировки в базе данных (например, PostgreSQL):**  
   ```sql
   SELECT * FROM pg_stat_activity WHERE state = 'active';
   ```
5. **Проверить использование ресурсов:**  
   ```sh
   kubectl top pod -n my-app
   ```

### **Решение:**  
- **Оптимизировать код** для устранения дедлоков  
- **Настроить Readiness Probe**, чтобы Kubernetes не отправлял трафик в зависшие поды:  
  ```yaml
  readinessProbe:
    httpGet:
      path: /healthz
      port: 8080
    initialDelaySeconds: 5
    periodSeconds: 10
  ```
- **Включить автоматический рестарт зависших подов:**  
  ```yaml
  restartPolicy: Always
  ```

---

## 8️⃣ **Проблема с DNS в EKS: Поды не могут резолвить домены**  

### **Описание кейса:**  
Некоторые поды в EKS не могут обращаться к внешним сервисам по DNS.  

### **Как расследовать проблему?**  
1. **Проверить DNS-конфигурацию в поде:**  
   ```sh
   kubectl exec -it <pod-name> -- cat /etc/resolv.conf
   ```
2. **Протестировать DNS-запросы:**  
   ```sh
   kubectl exec -it <pod-name> -- nslookup google.com
   ```
3. **Посмотреть конфигурацию CoreDNS:**  
   ```sh
   kubectl get pods -n kube-system | grep coredns
   kubectl logs -f <coredns-pod> -n kube-system
   ```
4. **Проверить Network Policies:**  
   ```sh
   kubectl get networkpolicy -A
   ```
5. **Проверить Security Groups и разрешенные порты:**  
   ```sh
   aws ec2 describe-security-groups --group-id sg-xxxxxx
   ```

### **Решение:**  
- **Перезапустить CoreDNS:**  
  ```sh
  kubectl rollout restart deployment coredns -n kube-system
  ```
- **Добавить рекурсивный DNS-сервер в ConfigMap:**  
  ```yaml
  apiVersion: v1
  kind: ConfigMap
  metadata:
    name: coredns
    namespace: kube-system
  data:
    Corefile: |
      .:53 {
        errors
        health
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          fallthrough in-addr.arpa ip6.arpa
        }
        forward . 8.8.8.8 1.1.1.1
        cache 30
        loop
        reload
        loadbalance
      }
  ```
- **Проверить ограничение max-udp-packet-size:**  
  ```sh
  kubectl set env deployment/coredns -n kube-system MAX_UDP_SIZE=4096
  ```

---

## 9️⃣ **Сетевая проблема: Под не может достучаться до другого пода по сервису**  

### **Описание кейса:**  
Одно приложение внутри кластера не может обратиться к другому через **ClusterIP Service**.  

### **Как расследовать проблему?**  
1. **Проверить, запущены ли оба пода:**  
   ```sh
   kubectl get pods -n my-app
   ```
2. **Проверить, есть ли сервис:**  
   ```sh
   kubectl get svc -n my-app
   ```
3. **Попробовать обратиться к сервису внутри пода:**  
   ```sh
   kubectl exec -it <pod-name> -n my-app -- curl <service-name>:<port>
   ```
4. **Проверить IP-адреса и маршрутизацию:**  
   ```sh
   kubectl get endpoints -n my-app
   ```
5. **Проверить сетевые политики:**  
   ```sh
   kubectl get networkpolicy -n my-app
   ```

### **Решение:**  
- **Отключить Network Policies для тестирования:**  
  ```sh
  kubectl delete networkpolicy my-network-policy -n my-app
  ```
- **Убедиться, что в Service правильно указан selector:**  
  ```yaml
  selector:
    app: my-app
  ```
- **Если используется AWS CNI, проверить IP-адреса:**  
  ```sh
  aws eks describe-cluster --name my-cluster --query cluster.resourcesVpcConfig
  ```

---

## 🔟 **Helm-релиз сломался после обновления**  

### **Описание кейса:**  
Вы обновили Helm-релиз, и теперь приложение не работает.  

### **Как расследовать проблему?**  
1. **Посмотреть статус Helm-релиза:**  
   ```sh
   helm list -n my-app
   ```
2. **Посмотреть логи Helm:**  
   ```sh
   helm history my-app -n my-app
   ```
3. **Посмотреть разницу между версиями:**  
   ```sh
   helm diff upgrade my-app my-repo/my-chart -n my-app
   ```
4. **Посмотреть логи подов:**  
   ```sh
   kubectl logs -l app=my-app -n my-app
   ```

### **Решение:**  
- **Откатить релиз:**  
  ```sh
  helm rollback my-app 1 -n my-app
  ```
- **Проверить шаблоны перед деплоем:**  
  ```sh
  helm template my-app my-repo/my-chart --values values.yaml
  ```
- **Использовать `--atomic`, чтобы избежать некорректных деплоев:**  
  ```sh
  helm upgrade --install my-app my-repo/my-chart -n my-app --atomic
  ```

---

## 1️⃣1️⃣ **Проблема с IAM: Под не может получить доступ к S3**  

### **Описание кейса:**  
Приложение в EKS должно загружать файлы в **Amazon S3**, но получает `403 Forbidden`.  

### **Как расследовать проблему?**  
1. **Проверить IAM Role у пода:**  
   ```sh
   kubectl get serviceaccount -n my-app
   ```
2. **Проверить IAM-политику:**  
   ```sh
   aws iam get-policy --policy-arn arn:aws:iam::123456789012:policy/MyPolicy
   ```
3. **Проверить, есть ли доступ у роли:**  
   ```sh
   aws sts get-caller-identity
   ```

### **Решение:**  
- **Использовать IAM Roles for Service Accounts (IRSA):**  
  ```yaml
  apiVersion: eksctl.io/v1alpha5
  kind: ClusterConfig
  metadata:
    name: my-cluster
    region: us-east-1
  iam:
    withOIDC: true
  ```
- **Создать IAM-политику для S3:**  
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": "s3:*",
        "Resource": "arn:aws:s3:::my-bucket/*"
      }
    ]
  }
  ```
- **Применить роль к Service Account:**  
  ```sh
  eksctl create iamserviceaccount --name my-sa --namespace my-app \
    --cluster my-cluster --attach-policy-arn arn:aws:iam::123456789012:policy/MyPolicy \
    --approve
  ```

---

Эти кейсы помогут вам подготовиться к **реальным проблемам в EKS**. Если нужны еще примеры – спрашивайте! 🚀


Вот еще несколько сложных кейсов для **EKS DevOps Engineer** в формате **Markdown**.  

---

# 🔥 Дополнительные сложные кейсы для **EKS DevOps Engineer**

## 1️⃣2️⃣ **Проблема с ingress: Nginx Ingress Controller не работает**  

### **Описание кейса:**  
Вы настроили **Nginx Ingress Controller** для балансировки нагрузки, но он не отвечает на запросы.  

### **Как расследовать проблему?**  
1. **Проверить статус контроллера:**  
   ```sh
   kubectl get pods -n ingress-nginx
   ```
2. **Посмотреть логи Nginx Ingress Controller:**  
   ```sh
   kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx
   ```
3. **Проверить настройки Ingress:**  
   ```sh
   kubectl get ingress -n my-app
   kubectl describe ingress my-ingress -n my-app
   ```
4. **Посмотреть, правильно ли работает Service LoadBalancer:**  
   ```sh
   kubectl get svc -n ingress-nginx
   ```
5. **Проверить Security Groups в AWS:**  
   ```sh
   aws ec2 describe-security-groups --group-id sg-xxxxx
   ```

### **Решение:**  
- **Перезапустить Nginx Ingress Controller:**  
  ```sh
  kubectl rollout restart deployment ingress-nginx-controller -n ingress-nginx
  ```
- **Добавить аннотации для AWS ALB (если используется):**  
  ```yaml
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
  ```
- **Убедиться, что все необходимые порты (80, 443) открыты в Security Group.**

---

## 1️⃣3️⃣ **EKS cluster-autoscaler не создает новые ноды**  

### **Описание кейса:**  
Приложение требует больше подов, но **Cluster Autoscaler** не увеличивает количество нод.  

### **Как расследовать проблему?**  
1. **Проверить логи Cluster Autoscaler:**  
   ```sh
   kubectl logs -f deployment/cluster-autoscaler -n kube-system
   ```
2. **Посмотреть лимиты нод-группы:**  
   ```sh
   aws eks describe-nodegroup --cluster-name my-cluster --nodegroup-name my-nodegroup
   ```
3. **Проверить, какие поды не могут запуститься:**  
   ```sh
   kubectl get pods --field-selector=status.phase=Pending -o wide
   ```
4. **Проверить ресурсы:**  
   ```sh
   kubectl describe node <node-name>
   kubectl top node
   ```

### **Решение:**  
- **Обновить конфигурацию Cluster Autoscaler:**  
  ```yaml
  - --nodes=2:10:my-nodegroup
  ```
- **Увеличить `maxSize` в AWS Auto Scaling Group:**  
  ```sh
  aws autoscaling update-auto-scaling-group \
    --auto-scaling-group-name my-asg --max-size 10
  ```
- **Использовать Karpenter вместо Cluster Autoscaler.**

---

## 1️⃣4️⃣ **Проблема с EFS: Приложение зависает при монтировании**  

### **Описание кейса:**  
Вы используете **AWS EFS** как persistent storage, но приложение зависает при монтировании.  

### **Как расследовать проблему?**  
1. **Проверить статус монтирования:**  
   ```sh
   kubectl describe pod <pod-name>
   ```
2. **Проверить логи драйвера EFS CSI:**  
   ```sh
   kubectl logs -n kube-system -l app=efs-csi-driver
   ```
3. **Проверить, смонтирована ли файловая система:**  
   ```sh
   df -h | grep efs
   ```
4. **Посмотреть, правильно ли настроен `StorageClass`:**  
   ```sh
   kubectl get storageclass
   ```

### **Решение:**  
- **Использовать `efs-utils` для диагностики:**  
  ```sh
  sudo amazon-linux-extras enable efs-utils
  sudo yum install -y amazon-efs-utils
  ```
- **Правильно настроить `PersistentVolumeClaim`:**  
  ```yaml
  apiVersion: v1
  kind: PersistentVolumeClaim
  metadata:
    name: efs-claim
  spec:
    accessModes:
      - ReadWriteMany
    storageClassName: efs-sc
  ```

---

## 1️⃣5️⃣ **Kubernetes Jobs не завершаются и зависают в Running**  

### **Описание кейса:**  
Вы запустили **Kubernetes Job**, но он не завершается.  

### **Как расследовать проблему?**  
1. **Проверить статус Job:**  
   ```sh
   kubectl get jobs -n my-app
   ```
2. **Посмотреть логи подов:**  
   ```sh
   kubectl logs -l job-name=my-job -n my-app
   ```
3. **Проверить, настроен ли лимит перезапусков:**  
   ```sh
   kubectl describe job my-job -n my-app
   ```

### **Решение:**  
- **Добавить лимит попыток:**  
  ```yaml
  spec:
    backoffLimit: 3
  ```
- **Использовать `activeDeadlineSeconds`:**  
  ```yaml
  spec:
    activeDeadlineSeconds: 600
  ```
- **Удалить зависшие Job-ресурсы:**  
  ```sh
  kubectl delete job my-job --force --grace-period=0 -n my-app
  ```

---

## 1️⃣6️⃣ **Проблема с HPA: Horizontal Pod Autoscaler не работает**  

### **Описание кейса:**  
**HPA** не масштабирует поды, даже при высокой нагрузке.  

### **Как расследовать проблему?**  
1. **Проверить статус HPA:**  
   ```sh
   kubectl get hpa -n my-app
   ```
2. **Посмотреть, доступны ли метрики:**  
   ```sh
   kubectl top pod -n my-app
   ```
3. **Проверить, работает ли Metrics Server:**  
   ```sh
   kubectl get deployment -n kube-system metrics-server
   ```

### **Решение:**  
- **Перезапустить Metrics Server:**  
  ```sh
  kubectl rollout restart deployment metrics-server -n kube-system
  ```
- **Убедиться, что включен API-агрегатор:**  
  ```sh
  kubectl get apiservices | grep metrics
  ```
- **Использовать Prometheus + KEDA вместо стандартного HPA.**

---

## 1️⃣7️⃣ **AWS ALB Ingress Controller не создает LoadBalancer**  

### **Описание кейса:**  
Вы используете **AWS Load Balancer Controller**, но ALB не создается.  

### **Как расследовать проблему?**  
1. **Проверить логи контроллера:**  
   ```sh
   kubectl logs -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
   ```
2. **Посмотреть аннотации Ingress:**  
   ```sh
   kubectl describe ingress my-ingress -n my-app
   ```
3. **Проверить IAM-роли:**  
   ```sh
   aws iam get-role --role-name eks-alb-controller-role
   ```
4. **Посмотреть события Ingress:**  
   ```sh
   kubectl get events -n my-app
   ```

### **Решение:**  
- **Перезапустить AWS Load Balancer Controller:**  
  ```sh
  kubectl rollout restart deployment aws-load-balancer-controller -n kube-system
  ```
- **Добавить аннотации к Ingress:**  
  ```yaml
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
  ```
- **Убедиться, что IAM Role содержит нужные политики.**

---

Эти **реальные кейсы** помогут подготовиться к **сложным задачам в EKS**. Если нужны еще примеры – спрашивайте! 🚀


