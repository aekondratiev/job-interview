### 🚀 **Установка AWS Load Balancer Controller в EKS**  
AWS Load Balancer Controller нужен для автоматического создания **ALB (Application Load Balancer)** и **NLB (Network Load Balancer)** в **Amazon EKS**.

---

## **📌 1. Установи `eksctl` и `kubectl` (если не установлены)**
Если у тебя их нет, установи:

```sh
# Устанавливаем eksctl
curl -sSL "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_Linux_amd64" -o /usr/local/bin/eksctl
chmod +x /usr/local/bin/eksctl

# Устанавливаем kubectl
curl -o kubectl https://s3.us-west-2.amazonaws.com/amazon-eks/1.27.0/2023-11-27/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv kubectl /usr/local/bin/
```

Проверь, что всё работает:
```sh
eksctl version
kubectl version --client
```

---

## **📌 2. Создаём IAM-политику для Load Balancer Controller**
Load Balancer Controller требует IAM-ролей для управления балансировщиками.

### **(1) Создаём IAM-политику**
```sh
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json

aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

Сохрани **Arn политики**, он понадобится далее (выглядит как `arn:aws:iam::123456789012:policy/AWSLoadBalancerControllerIAMPolicy`).

---

### **(2) Создаём IAM-роль и привязываем её к узлам**
Запусти команду, заменив `<CLUSTER_NAME>` на имя твоего кластера:

```sh
eksctl create iamserviceaccount \
  --cluster <CLUSTER_NAME> \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --attach-policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --override-existing-serviceaccounts \
  --approve
```

Проверь, что сервис-аккаунт создался:
```sh
kubectl get serviceaccount -n kube-system | grep aws-load-balancer-controller
```

---

## **📌 3. Устанавливаем AWS Load Balancer Controller**
Используем **Helm** для установки.

### **(1) Добавляем Helm-репозиторий**
```sh
helm repo add eks https://aws.github.io/eks-charts
helm repo update
```

### **(2) Устанавливаем AWS Load Balancer Controller**
Замените `<CLUSTER_NAME>` на имя вашего EKS-кластера:
```sh
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=<CLUSTER_NAME> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

### **(3) Проверяем, что контроллер работает**
```sh
kubectl get deployment -n kube-system aws-load-balancer-controller
```
Если всё установилось правильно, статус будет `AVAILABLE`.

---

## **📌 4. Применение LoadBalancer для сервиса**
Теперь можно создать `Ingress` или `Service` типа `LoadBalancer`. Например:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: example-ingress
  namespace: default
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}]'
spec:
  ingressClassName: alb
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

Применяем:
```sh
kubectl apply -f ingress.yaml
```

---

## **🔥 Итог**
Теперь у тебя **AWS Load Balancer Controller** работает в **EKS** и может автоматически создавать **ALB/NLB**.  

✅ **Автоматическое управление AWS ALB/NLB**  
✅ **Поддержка HTTPS/SSL и правил маршрутизации**  
✅ **Гибкость через аннотации (`alb.ingress.kubernetes.io/...`)**  

Если тебе нужно **HTTPS, internal ALB или NLB**, скажи – помогу настроить! 🚀


