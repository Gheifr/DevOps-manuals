K8s Manifest Structure

Кожен YAML-файл Kubernetes містить опис одного або кількох ресурсів. Мінімальна структура одного ресурсу має вигляд:

```yaml
apiVersion: <версія API>       # обов'язкове. Визначає версію API, до якого належить ресурс
kind: <тип ресурсу>            # обов'язкове. Визначає тип ресурсу (Deployment, Service тощо)
metadata:                      # обов'язкове. Містить метадані об'єкта
  name: <ім'я>                 # обов'язкове. Унікальне ім'я об'єкта в межах простору імен
  namespace: <ім'я>            # не обов'язкове. Якщо не задано — default
  labels:                      # не обов'язкове. Для групування і вибірки об'єктів
    key: value
  annotations:                 # не обов'язкове. Для зберігання довільних метаданих
    key: value
spec:                          # обов'язкове для більшості ресурсів. Специфікація поведінки
```

---

## Документ 1: Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: nginx:1.25
          ports:
            - containerPort: 80
```

---

## Документ 2: Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: example-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## Документ 3: ConfigMap

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-config
data:
  APP_MODE: production
  LOG_LEVEL: info
```

---

## Документ 4: Secret

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-secret
type: Opaque
data:
  DB_PASSWORD: cGFzc3dvcmQ=
```

---

## Документ 5: PersistentVolume (PV)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: example-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: manual
  hostPath:
    path: /mnt/data
```

---

## Документ 6: PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: example-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: manual
```

---

## Документ 7: StatefulSet

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: example-statefulset
spec:
  serviceName: example-headless
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: redis
  volumeClaimTemplates:
    - metadata:
        name: data
      spec:
        accessModes:
          - ReadWriteOnce
        resources:
          requests:
            storage: 1Gi
```

---

## Документ 8: DaemonSet

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: example-daemonset
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
      labels:
        app: monitoring-agent
    spec:
      containers:
        - name: agent
          image: busybox
          command: ["sh", "-c", "while true; do echo hello; sleep 10; done"]
```

---


