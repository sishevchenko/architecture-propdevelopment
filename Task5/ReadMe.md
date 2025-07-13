# Управление трафиком внутри кластера Kubertnetes

## Разделы

- [Введение](#введение)
- [Что нужно сделать](#что-нужно-сделать)
- [Настройка сетевых политик в кластере Kubernetes](#настройка-сетевых-политик-в-кластере-kubernetes)
    1. [Создание отдельного namespace](#1-создание-отдельного-namespace)
    2. [Настройка подов основного приложения](#2-настройка-подов-основного-приложения)
    3. [Настройка подов консоли админа](#3-настройка-подов-консоли-админа)
    4. [Настройка сетевых политик основоного приложения](#4-настройка-сетевых-политик-основоного-приложения)
    5. [Настройка сетевых политик консоли админа](#5-настройка-сетевых-политик-консоли-админа)

## Введение

В этом задании вам нужно разграничить трафик между сервисами, которые развёрнуты
в кластере Kubernetes:

- Вам необходимо добавить новый сервис. В терминах Kubernetes это под (pod). При
этом нужно запретить другим подам с ним взаимодействовать.
- Необходимо изолировать трафик к новому сервису от других подов.

## Что нужно сделать

1. В кластере, внутри одного namespace, вам необходимо развернуть четыре сервиса.
В качестве самого сервиса используйте образ Nginx (вам не нужно создавать логику
приложения в этом заданий).

2. Назначьте метки для сервисов. Метки выполняют функцию ролей для сервиса:
    - `front-end`
    - `back-end-api`
    - `admin-front-end`
    - `admin-back-end-api`

3. Создайте сетевые политики. Настройте сетевые политики так, чтобы разделить
трафик между сервисами API (`admin-back-end-api`, `back-end-api`) и
сервисами, которые используют их UI (`front-end`, `admin-front-end`).
Таким образом, сетевые политики должны разрешить сетевой трафик в обе стороны
между парой сервисов `front-end` и `back-end-api`, а также
`admin-front-end` и `admin-back-end-api`.

## Настройка сетевых политик в кластере Kubernetes

### 1. Создание отдельного namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
name: sprint7-5
```

### 2. Настройка подов основного приложения

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: front-end-app
namespace: sprint7-5
spec:
replicas: 1
selector:
    matchLabels:
    role: front-end
template:
    metadata:
    labels:
        role: front-end
    spec:
    containers:
    - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
name: back-end-app
namespace: sprint7-5
spec:
replicas: 1
selector:
    matchLabels:
    role: back-end-api
template:
    metadata:
    labels:
        role: back-end-api
    spec:
    containers:
    - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

### 3. Настройка подов консоли админа

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
name: admin-front-end-app
namespace: sprint7-5
spec:
replicas: 1
selector:
    matchLabels:
    role: admin-front-end
template:
    metadata:
    labels:
        role: admin-front-end
    spec:
    containers:
    - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: apps/v1
kind: Deployment
metadata:
name: admin-back-end-app
namespace: sprint7-5
spec:
replicas: 1
selector:
    matchLabels:
    role: admin-back-end-api
template:
    metadata:
    labels:
        role: admin-back-end-api
    spec:
    containers:
    - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

### 4. Настройка сетевых политик основоного приложения

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-frontend-to-backend
namespace: sprint7-5
spec:
podSelector:
    matchLabels:
    role: back-end-api
policyTypes:
    - Ingress
    - Egress
ingress:
    - from:
    - podSelector:
        matchLabels:
            role: front-end
egress:
    - to:
    - podSelector:
        matchLabels:
            role: front-end
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-backend-to-frontend
namespace: sprint7-5
spec:
podSelector:
    matchLabels:
    role: front-end
policyTypes:
    - Ingress
    - Egress
ingress:
    - from:
    - podSelector:
        matchLabels:
            role: back-end-api
egress:
    - to:
    - podSelector:
        matchLabels:
            role: back-end-api
```

### 5. Настройка сетевых политик консоли админа

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-admin-frontend-to-admin-backend
namespace: sprint7-5
spec:
podSelector:
    matchLabels:
    role: admin-back-end-api
policyTypes:
    - Ingress
    - Egress
ingress:
    - from:
    - podSelector:
        matchLabels:
            role: admin-front-end
egress:
    - to:
    - podSelector:
        matchLabels:
            role: admin-front-end
---
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
name: allow-admin-backend-to-admin-frontend
namespace: sprint7-5
spec:
podSelector:
    matchLabels:
    role: admin-front-end
policyTypes:
    - Ingress
    - Egress
ingress:
    - from:
    - podSelector:
        matchLabels:
            role: admin-back-end-api
egress:
    - to:
    - podSelector:
        matchLabels:
            role: admin-back-end-api
```

[<- На главную страницу](../ReadMe.md)
