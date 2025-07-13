# Защита доступа к кластеру Kubernetes

## Разделы

- [Список ролей](#список-ролей)
- [Спикок пользователей](#спикок-пользователей)
- [Скрипты Kubernates](#скрипты-kubernates)
  - [Namespaces](#namespaces)
  - [Doorlock Roles](#doorlock-roles)
  - [Doorlock Users](#doorlock-users)
  - [Barrier Roles](#barrier-roles)
  - [Barrier Users](#barrier-users)

## Список ролей

| Роль                    | Права роли                                      | Группы пользователей                             |
| :---------------------- | :---------------------------------------------- | :----------------------------------------------- |
| doorlock-pod-monitor    | get, list, watch                                | системы мониторинга                              |
| doorlock-pod-maintainer | get, list, watch, create, update, patch         | пользователи с возможностью управления ресурсами |
| doorlock-pod-admin      | get, list, watch, create, update, patch, delete | администраторы                                   |
| doorlock-secrets        | get, list                                       | доступ к секретам                                |
| barrier-pod-monitor     | get, list, watch                                | системы мониторинга                              |
| barrier-pod-maintainer  | get, list, watch, create, update, patch         | пользователи с возможностью управления ресурсами |
| barrier-pod-admin       | get, list, watch, create, update, patch, delete | администраторы                                   |
| barrier-secrets         | get, list                                       | доступ к секретам                                |

## Спикок пользователей

| Пользователь  | Роли                                      |
| :------------ | :---                                      |
| uncle-ahoo    | doorlock-pod-monitor, barrier-pod-monitor |
| cheburashka   | doorlock-pod-maintainer                   |
| gena          | doorlock-pod-admin                        |
| shapoklyak    | doorlock-secrets                          |
| sharik        | barrier-pod-maintainer                    |
| matroskin     | barrier-pod-admin                         |
| pechkin       | barrier-secrets                           |

## Скрипты Kubernates

### Namespaces

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: sprint7-doorlocks
---
apiVersion: v1
kind: Namespace
metadata:
  name: sprint7-doorlocks-secure-space
---
apiVersion: v1
kind: Namespace
metadata:
  name: sprint7-barriers
---
apiVersion: v1
kind: Namespace
metadata:
  name: sprint7-barriers-secure-space
```

### Doorlock Roles

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-doorlocks
  name: doorlock-pod-monitor
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-doorlocks
  name: doorlock-pod-maintainer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "patch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-doorlocks
  name: doorlock-pod-admin
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-doorlocks-secure-space
  name: doorlock-secrets
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "list"] 
```

### Doorlock Users

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: sprint7-doorlocks
subjects:
- kind: User
  name: uncle-ahoo
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: doorlock-pod-monitor
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: manage-pods
  namespace: sprint7-doorlocks
subjects:
- kind: User
  name: cheburashka
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: doorlock-pod-maintainer
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: administer-pods
  namespace: sprint7-doorlocks
subjects:
- kind: User
  name: gena
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: doorlock-pod-admin
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: secrets-pods
  namespace: sprint7-doorlocks-secure-space
subjects:
- kind: User
  name: shapoklyak
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: doorlock-secrets
  apiGroup: rbac.authorization.k8s.io
```

### Barrier Roles

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-barriers
  name: barrier-pod-monitor
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-barriers
  name: barrier-pod-maintainer
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "patch"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-barriers
  name: barrier-pod-admin
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: sprint7-barriers-secure-space
  name: barrier-secrets
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "list"] 
```

### Barrier Users

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: sprint7-barriers
subjects:
- kind: User
  name: uncle-ahoo
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: barrier-pod-monitor
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: manage-pods
  namespace: sprint7-barriers
subjects:
- kind: User
  name: sharik
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: barrier-pod-maintainer
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: administer-pods
  namespace: sprint7-barriers
subjects:
- kind: User
  name: matroskin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: barrier-pod-admin
  apiGroup: rbac.authorization.k8s.io
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: secrets-pods
  namespace: sprint7-barriers-secure-space
subjects:
- kind: User
  name: pechkin
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: barrier-secrets
  apiGroup: rbac.authorization.k8s.io
```

[<- На главную страницу](../ReadMe.md)
