#kubernetes #cloud #yandex

# 🧪 Kubernetes / Containers / Network

^e07136
## Комплексная стратегия харденинга и минимизации рисков в облачных и контейнерных инфраструктурах: Практическое руководство и дорожная карта (2025-2026)

> Современная архитектура информационных систем перешла к распределенным микросервисным средам, управляемым через Kubernetes в облаках. Это расширило поверхность атаки, требуя укрепления систем на всех уровнях — от ядра Linux до облачного IAM. Данный отчет представляет механизмы защиты и структурированную дорожную карту для обучения.

## Глава 1: Фундаментальный харденинг операционной системы Linux

Безопасность облака начинается с ОС Linux. Харденинг — это процесс уменьшения поверхности атаки через минимизацию ПО и строгую настройку прав.

### 1.1. Права доступа rwx и ACL

Система битов `rwx` (read, write, execute) — база безопасности. Ошибки в правах на директории часто ведут к компрометации данных. Для защиты рекомендуется принцип наименьших привилегий и использование `Sticky Bit` (например, в `/tmp`), чтобы только владелец мог удалять файлы. Списки ACL позволяют настраивать гранулярный доступ для конкретных пользователей.

### 1.2. Безопасность sudo

Некорректная конфигурация в `/etc/sudoers` является критическим риском. Уязвимости типа CVE-2019-14287 показывают, что ошибки в обработке UID могут обходить запреты. Современный харденинг включает отказ от `ALL=(ALL:ALL) ALL`, использование `requiretty` и обязательный аудит вызовов.

## Глава 2: Изоляция контейнеров и ядерные примитивы

Контейнеры используют ядро хоста, поэтому механизмы изоляции критичны для предотвращения побегов (container escape).

- **Namespaces (Пространства имен):** Виртуализируют ресурсы (PID, NET, MNT, USER), создавая иллюзию изолированной системы.
    
- **Cgroups (Контрольные группы):** Ограничивают потребление ресурсов (CPU, память, PIDs), предотвращая DoS-атаки типа "fork bomb".
    
- **Seccomp и Capabilities:** Seccomp фильтрует системные вызовы ядра, а Capabilities разделяют права root на мелкие части, позволяя выдавать только нужные (например, `CAP_NET_BIND_SERVICE`).
    

## Глава 3: Продвинутые песочницы: gVisor и MicroVM

Для запуска недоверенного кода (например, ИИ-агентов) стандартных контейнеров недостаточно.

- **gVisor:** Перехватывает системные вызовы в пользовательском пространстве, пропуская к ядру хоста только около 20 безопасных вызовов.
    
- **MicroVM (Kata, Firecracker):** Запускают каждый контейнер в отдельной облегченной виртуальной машине с собственным ядром, обеспечивая аппаратную изоляцию.
    

## Глава 4: Безопасность и эксплуатация Kubernetes

Kubernetes требует защиты Control Plane, etcd и рабочих нагрузок.

- **Сетевые политики:** По умолчанию в K8s все поды видят друг друга. Модель Zero Trust требует внедрения `Network Policies` для ограничения трафика.
    
- **RBAC и Admission Control:** Ошибки в правах сервисных аккаунтов — частая причина взломов. Инструменты вроде `Kyverno` или `OPA` блокируют создание небезопасных ресурсов.
    

## Глава 5: Kubernetes в облачных средах (EKS, GKE, AKS)

Работа в облаке переносит фокус на интеграцию K8s с инфраструктурой провайдера.

- **Облачный IAM:** В облаке личность (Identity) — это новый периметр. Использование `Workload Identity` позволяет подам получать доступ к облачным ресурсам (S3, BigQuery) без статических ключей.
    
- **Сетевая интеграция:** Облачные K8s используют нативные VPC и балансировщики. Важно настраивать `Private Google Access` или `AWS PrivateLink` для изоляции трафика внутри облака.
    
- **Специфические угрозы:** Атаки через сервис метаданных (IMDSv2) в AWS могут привести к краже ролей узла.
    

## Глава 6: Дорожная карта обучения (Roadmap)

### Уровень 1: Основы Linux и права доступа. Covered in [[🏴Cbs intermediate roadmap#^5207fe|intermediate roadmap]]

- [x] **Linux Fundamentals (TryHackMe)**: Практика `chmod`, `chown` и структуры системы. [Лаба](https://tryhackme.com/module/linux-fundamentals) ✅ 2026-04-03
    
- [ ] **Agent Sudo (TryHackMe)**: Взлом и защита настроек sudo. [Лаба](https://tryhackme.com/room/agentsudoctf)
    
- [ ] **Scenario "La Rinconada" (SadServers)**: LIVE-задача на эксплуатацию sudo для получения root. [Лаба](https://sadservers.com/scenarios)

- [ ] SadServers: "Taipei" — хакинг и настройка Firewall (Port Knocking). Ссылка: sadservers.com/scenario/taipei

### Уровень 2: Контейнеры и Песочницы

* First steps:
* [ ] https://k8sgames.com - #kubernetes in-browser game (also look up materials in [[#^8d161b|links]])
* [ ] https://youtu.be/TlHvYWVUZyc?si=rqzJ7KCWdv6YYiPZ - brief explanation of k8s
* [ ] https://training.play-with-kubernetes.com/ - play with k8s
- [ ] **Container Hardening (TryHackMe)**: Пошаговая настройка защиты Docker. [Лаба](https://tryhackme.com/room/containerhardening) PAID - найти аналог
    
- [ ] **Namespaces & Cgroups (Medium/GitHub)**: Ручное создание контейнера через `unshare`. [Лаба](https://github.com/TamimEhsan/Simple-Sandbox)
    
- [ ] **Sandbox gVisor (Killercoda)**: Запуск изолированных подов в K8s через gVisor. [Лаба](https://killercoda.com/killer-shell-cks/scenario/sandbox-gvisor)
    

### Уровень 3: Kubernetes в Облаке (yandex/EKS/GKE) — Администрирование и Основы
#### For yandex k8s is covered [[☁️cloud CbS roadmap#^9737c2|here]]
#### optional
- [ ] **Google Cloud IAM & Networking for AWS Professionals (Coursera)**: Изучение иерархии ресурсов и VPC в контексте K8s. [Лаба](https://www.coursera.org/learn/gcp-fundamentals-aws)
    
- [ ] **Large Language Model (LLM) on Amazon EKS (AWS Workshop Studio)**: Развертывание реальных нагрузок в EKS. [Лаба](https://builder.aws.com/build/workshops)
    
- [ ] **Cloud-Native SD-WAN with K8s (Cisco DevNet)**: Интеграция кластера с корпоративной сетью. [Лаба](https://developer.cisco.com/learning/search/?contentType=lab&page=1)
    
- [ ] **Develop GenAI Apps (GCP Skills Boost)**: Деплой контейнеризированных приложений в облако. [Лаба](https://www.cloudskillsboost.google/course_templates/735)
    

### Уровень 4: Безопасность Kubernetes (CKS)

- [ ] https://tryhackme.com/room/introtocontainerisation
- [ ] https://tryhackme.com/module/kubernetes-hardening
* [ ] https://tryhackme.com/room/kubernetesforyouly
* [ ] https://tryhackme.com/room/insekube

- [ ] **Kubernetes Goat**: Симуляция побегов из контейнера и атак на кластер. [Лаба](https://madhuakula.com/kubernetes-goat/)
    
- [ ] **Network Policies (Killercoda)**: Создание "Default Deny" политик. [Лаба](https://killercoda.com/killer-shell-cks/scenario/networkpolicy-create-default-deny)
    
- [ ] **RBAC Least Privileges (Killercoda)**: Ограничение прав сервисных аккаунтов. [Лаба](https://killercoda.com/killer-shell-cks/scenario/rbac-serviceaccount-permissions)
    

### Уровень 5: LIVE-харденинг и Исключение рисков

- [ ] **Scenario "Saint John" (SadServers)**: Поиск процесса, забивающего диск логами (риск DoS). [Лаба](https://sadservers.com/scenarios)
    
- [ ] **Scenario "Bilbao" (SadServers)**: Исправление манифеста K8s пода, который не запускается. [Лаба](https://sadservers.com/scenarios)
    
- [ ] **Runtime Security with Falco (Killercoda)**: LIVE-мониторинг подозрительной активности в контейнерах. [Лаба](https://killercoda.com/killer-shell-cks/scenario/falco-change-rule)
    
- [ ] **Vulnerable Lambda (CloudGoat/AWS)**: Эксплуатация SSRF для кражи облачных секретов. [Лаба](https://github.com/ine-labs/AWSGoat)
    

# K8S, docker and Networks Courses & Labs 
## K8S games

^8d161b

* [ ] https://www.k8sgames
* [ ] https://learn.kodekloud.com/user/courses/kubernetes-challenges aka Game of Pods
* [ ] https://github.com/steadforce/k8s-escape-room/ - kubernetes escape room
---
## play with kubernetes
* [ ] https://training.play-with-kubernetes.com/
## Kubernetes Goat - k8s security labs
👉 https://github.com/madhuakula/kubernetes-goat  

### 💻 topics:
- Kubernetes misconfig  
- RBAC  
- container breakout  

## KillerCoda
- https://killercoda.com/

---

# 🧪 Cloud Security - Russian path
- [ ] https://tryhackme.com/room/cloudcomputingfundamentals
- [ ] https://tryhackme.com/room/cloudsecuritypitfalls
## yandex courses

^9737c2

^a0b546
* [ ] https://yandex.cloud/ru/training/ycloud?from=training-pro
* [ ] https://yandex.cloud/ru/training/infrastructure-protection?from=training-pro - protecting cloud infrestructure (ya.cloud)
* [ ] https://yandex.cloud/ru/training/network-security?from=training-pro
* [ ] https://yandex.cloud/ru/training/authentication?from=training-pro
* [ ] https://yandex.cloud/ru/training/appdef?from=training-pro
---
# Cloud Security - Western / international path:
## [Google Skills - hands on labs in real cloud](https://www.skills.google/catalog)

---
## 🟩 Cloud Security Labs (FREE)
👉 https://cloudlearn.io/labs  

📌:
- реальные cloud environments  
- AWS / Azure security :contentReference[oaicite:4]{index=4}  
---
## ☁️ EKS cluster Game
### CTF-like chalenges about cloud security in AWS EKS

* [ ] https://eksclustergames.com
---
## CloudGoat (AWS)

^885d58

- https://github.com/RhinoSecurityLabs/cloudgoat  

👉 сценарии:
- IAM privilege escalation
- S3 misconfig
- SSRF → metadata  

## AWSGoat
- https://github.com/ine-labs/AWSGoat  

## AWS Well-Architected Labs (Security)
- https://www.wellarchitectedlabs.com/security/  


## 🟪 AWS Free Tier
👉 https://aws.amazon.com/free  

---

## 🟦  Haxcamp (Cloud + Security)
👉 https://haxcamp.com/projects/

### 💻 темы:
- cloud security  
- AWS assessment  
- misconfig  

---

## 🟦 Microsoft Learn (sandbox)
👉 https://learn.microsoft.com/training  


---

# ⚔️ Offensive Security Additions

## 1. Cloud Infrastructure Attacks
* [ ] **IAM Privilege Escalation**: Identifying and exploiting over-privileged roles to gain Administrator access.
* [ ] **Storage Misconfigurations**: Exploiting public S3 buckets, Azure Blobs, and Google Cloud Storage for data exfiltration.
* [ ] **Metadata Service (IMDS) Attacks**: Exploiting SSRF to steal temporary cloud credentials from the instance metadata service (v1 and v2).

## 2. Kubernetes & Container Offensive
* [ ] **K8s RBAC Exploitation**: Using service account tokens to escalate privileges within a cluster.
* [ ] **Container Breakouts**: Escalating from a container to the host node using misconfigured capabilities or kernel exploits.
* [ ] **Attacking the K8s API**: Identifying exposed Kubelets and API servers for unauthorized access.

## 3. Serverless & CI/CD Offensive
* [ ] **Attacking Lambda/Functions**: Exploiting event-driven functions to gain a foothold in the cloud environment.
* [ ] **CI/CD Pipeline Poisoning**: Exploiting GitHub Actions or GitLab CI/CD to steal secrets and inject malicious code.

---

### 📚 Learning Resources
*   [**HackTricks Cloud**](https://cloud.hacktricks.xyz/) - The best free wiki for cloud exploitation techniques (AWS, Azure, GCP, Kubernetes).
*   [**Prowler (Open Source)**](https://github.com/prowler-cloud/prowler) - A free tool to audit cloud security. Learn by running it and analyzing findings.
*   [**CloudFox**](https://github.com/BishopFox/cloudfox) - A free tool for cloud penetration testing. Excellent for enumeration.

### 🛡️ CI/CD & DevSecOps
*   [**CI/CD Goat**](https://github.com/cider-security-research/cicd-goat) - A free, self-hosted lab to learn about vulnerabilities in CI/CD pipelines (Jenkins, GitHub Actions, etc.).
