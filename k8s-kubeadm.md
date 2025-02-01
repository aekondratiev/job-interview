## Kubernetes the kubeadm way

kubeadm — это утилита, предназначенная для упрощенной установки и настройки Kubernetes-кластера. В отличие от Kubernetes The Hard Way (где всё настраивается вручную), kubeadm автоматизирует многие процессы, но оставляет достаточно гибкости для кастомизации.

### Основные компоненты
Когда вы создаете кластер с помощью kubeadm, он устанавливает и настраивает ключевые компоненты Kubernetes:
- API Server — центральный компонент для управления кластером.
- etcd — распределенное хранилище состояний кластера.
- Controller Manager — управляет контроллерами (например, ReplicaSet, Node Controller).
- Scheduler — распределяет поды по узлам.
- Kubelet — агент на каждом узле, который запускает контейнеры.
- Kube Proxy — сетевой прокси для связи между подами.

### Установка кластера через kubeadm
#### Подготовка узлов
- Установить Docker (или другой совместимый контейнерный рантайм).
- Отключить swap: `sudo swapoff -a`
- Настроить сетевые параметры и IP forwarding.
#### Установка kubeadm, kubelet и kubectl
- sudo apt update && sudo apt install -y kubelet kubeadm kubectl
- sudo systemctl enable --now kubelet
#### Инициализация мастера
На главном узле: `sudo kubeadm init`
После успешной инициализации появится команда для подключения воркеров, например:
`kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>`
#### Настройка kubectl
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
#### Добавление воркеров
Выполнить команду kubeadm join на всех воркер-узлах.
#### Настройка сети
Необходимо развернуть сетевой плагин, например, Flannel:
```
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```
#### Заключение
Метод "the kubeadm way" — это баланс между автоматизацией и гибкостью. Он не требует сложной ручной настройки, но при этом позволяет настраивать компоненты по своему усмотрению.
