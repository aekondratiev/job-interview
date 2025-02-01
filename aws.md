Информация на тему **AWS Certified Solutions Architect**. Она охватывает ключевые темы, сервисы и концепции.

---

## **1. Основные концепции AWS**

### **Глобальная инфраструктура AWS**
- **Регионы (Regions)**: Географические локации (например, us-east-1, eu-west-1).
- **Зоны доступности (Availability Zones, AZ)**: Изолированные дата-центры внутри региона.
- **Edge Locations**: Пункты присутствия для ускорения доставки контента (используются CloudFront).

### **Модель ответственности (Shared Responsibility Model)**
- **AWS отвечает за**: Безопасность "облака" (инфраструктура, оборудование, ПО).
- **Клиент отвечает за**: Безопасность "в облаке" (данные, настройки, управление доступом).

---

## **2. Основные сервисы AWS**

### **Compute**
- **EC2 (Elastic Compute Cloud)**: Виртуальные серверы.
  - Типы инстансов: General Purpose, Compute Optimized, Memory Optimized и т.д.
  - Spot Instances: Дешевые, но могут быть прерваны.
- **Lambda**: Бессерверные функции.
  - Плата только за время выполнения.
  - Автоматическое масштабирование.

### **Storage**
- **S3 (Simple Storage Service)**: Объектное хранилище.
  - Классы хранения: Standard, Intelligent-Tiering, Glacier.
  - Функции: Versioning, Lifecycle Policies, Cross-Region Replication.
- **EBS (Elastic Block Store)**: Блочное хранилище для EC2.
  - Типы: gp2 (General Purpose), io1 (Provisioned IOPS), st1 (Throughput Optimized).
- **EFS (Elastic File System)**: Сетевое файловое хранилище.

### **Database**
- **RDS (Relational Database Service)**: Управляемые реляционные БД (MySQL, PostgreSQL, Aurora).
  - Функции: Multi-AZ, Read Replicas.
- **DynamoDB**: NoSQL база данных.
  - Высокая производительность, автоматическое масштабирование.
  - Модель оплаты: On-Demand или Provisioned Capacity.

### **Networking**
- **VPC (Virtual Private Cloud)**: Изолированная сеть в AWS.
  - Компоненты: Subnets, Route Tables, Internet Gateway, NAT Gateway.
- **Route 53**: DNS-сервис.
  - Функции: Routing Policies (Simple, Weighted, Latency, Failover).
- **CloudFront**: CDN для ускорения доставки контента.

### **Security**
- **IAM (Identity and Access Management)**: Управление доступом.
  - Политики (Policies): JSON-документы, определяющие разрешения.
  - Роли (Roles): Временные разрешения для сервисов или пользователей.
- **KMS (Key Management Service)**: Управление ключами шифрования.
- **Secrets Manager**: Хранение и ротация секретов.

---

## **3. Архитектурные принципы**

### **Well-Architected Framework**
1. **Операционное совершенство**: Автоматизация, мониторинг, CI/CD.
2. **Безопасность**: Защита данных, управление доступом, шифрование.
3. **Надежность**: Отказоустойчивость, Multi-AZ, автоматическое восстановление.
4. **Эффективность производительности**: Оптимизация ресурсов, выбор правильных сервисов.
5. **Экономическая эффективность**: Оптимизация затрат, использование Spot Instances, резервирование.

### **Паттерны проектирования**
- **Масштабирование**: Использование Auto Scaling, Load Balancer.
- **Отказоустойчивость**: Multi-AZ, Read Replicas, Backup & Restore.
- **Высокая доступность**: Использование нескольких AZ, Health Checks.

---

## **4. Масштабирование и балансировка нагрузки**

### **Auto Scaling**
- **EC2 Auto Scaling**: Автоматическое добавление/удаление EC2 инстансов.
  - Группы Auto Scaling (ASG): Минимальное, максимальное и желаемое количество инстансов.
- **Политики масштабирования**: Target Tracking, Step Scaling, Simple Scaling.

### **Load Balancing**
- **ALB (Application Load Balancer)**: Балансировка на уровне приложения (HTTP/HTTPS).
- **NLB (Network Load Balancer)**: Балансировка на транспортном уровне (TCP/UDP).
- **CLB (Classic Load Balancer)**: Устаревший сервис (не рекомендуется).

---

## **5. Мониторинг и логирование**

### **CloudWatch**
- **Метрики**: Мониторинг производительности (CPU, Memory, Network).
- **Логи**: Хранение и анализ логов.
- **Алармы**: Уведомления и автоматические действия на основе метрик.

### **CloudTrail**
- **Аудит**: Логирование всех API-вызовов в AWS.

---

## **6. Миграция и гибридные архитектуры**

### **Миграция**
- **AWS DMS (Database Migration Service)**: Миграция баз данных.
- **Snowball/Snowmobile**: Перенос больших объемов данных.

### **Гибридные архитектуры**
- **Direct Connect**: Выделенное сетевое соединение между локальной инфраструктурой и AWS.
- **Storage Gateway**: Интеграция локальных хранилищ с S3.

---

## **7. Оптимизация затрат**

### **Стратегии**
- **Reserved Instances**: Предоплата за EC2 инстансы со скидкой.
- **Savings Plans**: Гибкая модель скидок для EC2 и Lambda.
- **Spot Instances**: Использование неиспользуемых ресурсов AWS со скидкой.

### **Инструменты**
- **Cost Explorer**: Анализ и прогнозирование затрат.
- **Budgets**: Установка лимитов и уведомлений о затратах.

---

## **8. Вопросы на собеседовании**

1. **Как обеспечить высокую доступность приложения?**
   - Использование Multi-AZ, Auto Scaling, Load Balancer.

2. **Как защитить данные в S3?**
   - Включить Versioning, настроить шифрование (SSE-S3, SSE-KMS), использовать IAM Policies и Bucket Policies.

3. **В чем разница между ALB и NLB?**
   - ALB работает на уровне приложения (HTTP/HTTPS), NLB — на транспортном уровне (TCP/UDP).

4. **Как оптимизировать затраты на EC2?**
   - Использовать Reserved Instances, Spot Instances, Savings Plans.

5. **Как настроить автоматическое масштабирование?**
   - Создать Auto Scaling Group, настроить политики масштабирования (Target Tracking, Step Scaling).

---

