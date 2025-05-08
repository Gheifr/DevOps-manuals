## K8s Manifest Structure
### Зміст  
- [apiVersion & kind](#apiVersion-and-kind)  
- [Маніфест: Deployment](#Маніфест-Deployment)  
- [Маніфест: Service](#Маніфест-Service)  
- [Маніфест: ConfigMap](#Маніфест-ConfigMap)  
- [Маніфест: Secret](#Маніфест-Secret) 
- [Маніфест: PersistentVolume (PV)](#Маніфест-PersistentVolume)  
- [Маніфест: PersistentVolumeClaim (PVC)](#Маніфест-PersistentVolumeClaim)  
- [Маніфест: StatefulSet](#Маніфест-StatefulSet)  
- [Маніфест: DaemonSet](#Маніфест-DaemonSet)    


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
### apiVersion and kind  
#### Контроль об'єктів (контролери)  
kind|apiVersion|Опис|
:--- |:--- |:---|
Deployment|apps/v1|Масштабоване розгортання статeless подів|
StatefulSet|apps/v1|Розгортання збереження стану, унікальні pod-імена|
DaemonSet|apps/v1|Один pod на кожному вузлі|
ReplicaSet|apps/v1|Контролює кількість реплік pod|
Job|batch/v1|Одноразове виконання задачі|
CronJob|batch/v1|Переодичне виконання задачі за Cron|

#### Об’єкти зберігання (storage)  
kind|apiVersion|Опис
:--- |:--- |:---|
PersistentVolume|v1|Постійний том|
PersistentVolumeClaim|v1|Запит на том|
StorageClass|storage.k8s.io/v1|Налаштування сховища (динамічне provisoning)|
VolumeAttachment|storage.k8s.io/v1|Прив'язка тому до вузла (зазвичай CSI)|  

#### Мережеві ресурси
kind|apiVersion|Опис
:--- |:--- |:---|
Service|v1|Визначення доступу до подів
Ingress|networking.k8s.io/v1|Вхідний HTTP/HTTPS маршрут
NetworkPolicy|networking.k8s.io/v1|Політика мережевого доступу між подами


#### Конфігурація та секрети
kind|apiVersion|Опис
:--- |:--- |:---|
ConfigMap|v1|Неконфіденційні конфігураційні дані
Secret|v1|Конфіденційні дані (паролі, ключі)
ServiceAccount|v1|Обліковий запис пода для доступу до API

#### Системні об'єкти та управління
kind|apiVersion|Опис
:--- |:--- |:---|
Namespace|v1|Простір імен у кластері
Node|v1|Вузол (фізичний або віртуальний хост)
Pod|v1|Базовий об'єкт для контейнерів
LimitRange|v1|Обмеження ресурсів в Namespace
ResourceQuota|v1|Квоти ресурсів у Namespace
HorizontalPodAutoscaler|autoscaling/v2|Автоматичне масштабування pod'ів

#### Інші корисні ресурси
kind|apiVersion|Опис
:--- |:--- |:---|
Role|rbac.authorization.k8s.io/v1|Права доступу в Namespace
RoleBinding|rbac.authorization.k8s.io/v1|Прив’язка ролей до користувачів
ClusterRole|rbac.authorization.k8s.io/v1|Права на кластерному рівні
ClusterRoleBinding|rbac.authorization.k8s.io/v1|Прив’язка кластерної ролі

<details>
<summary>Примітки</summary>

- Більшість об’єктів типу kind: X мають apiVersion: v1, якщо це базові ресурси (Pod, ConfigMap, Secret тощо).

- Об’єкти, пов’язані з контролем, масштабуванням, зберіганням, мають окремі API групи (apps/, batch/, autoscaling/, networking/ тощо).

- Щоб уникнути помилок, завжди перевіряй apiVersion для свого Kubernetes-кластера командою:  
```bash
kubectl api-resources
```
</details>  

[До змісту](#Зміст)  

---


### Маніфест Deployment

```yaml
apiVersion: apps/v1                         # обов'язкове. Визначає версію API для Deployment
kind: Deployment                            # обов'язкове. Тип ресурсу — Deployment
metadata:                                   # обов'язкове. Містить метадані об'єкта
  name: example-deployment                  # обов'язкове. Унікальне ім'я об'єкта в кластері
spec:                                       # обов'язкове. Специфікація для Deployment
  replicas: 3                               # не обов'язкове. Кількість реплік подів
  selector:                                 # обов'язкове. Визначає, які поди керуються цим Deployment
    matchLabels:                            # обов'язкове. Пошук подів за мітками
      app: my-app
  template:                                 # обов'язкове. Шаблон для створення подів
    metadata:
      labels:                               # обов'язкове. Мітки, що застосовуються до подів
        app: my-app
    spec:                                   # обов'язкове. Описує конфігурацію пода
      containers:
        - name: my-container                # обов'язкове. Ім'я контейнера
          image: nginx:1.25                 # обов'язкове. Образ контейнера
          ports:
            - containerPort: 80             # не обов'язкове. Порт, що слухає контейнер
          volumeMounts:                     # не обов'язкове. Монтує том до контейнера
            - name: data-volume             # ім'я тому (має співпадати з іменем у volumes)
              mountPath: /usr/share/nginx/html  # шлях у контейнері, куди буде змонтовано том
      volumes:                              # не обов'язкове. Оголошення томів для пода
        - name: data-volume                 # ім'я тому (використовується у volumeMounts)
          persistentVolumeClaim:           # використовує існуючий PVC
            claimName: my-pvc               # ім'я PVC, який має бути створений окремо
```
<details>
<summary>Пояснення:</summary>  

**apiVersion**: Версія API для Deployment ресурсу.

**kind**: Тип ресурсу — Deployment, який забезпечує автоматичне масштабування та оновлення подів.

**metadata**: Містить унікальні метадані, такі як ім'я Deployment.

**spec**: Специфікація для налаштування Deployment, включаючи кількість реплік, мітки для вибору подів і шаблон для створення подів.

&nbsp;&nbsp;&nbsp;&nbsp;**replicas**: Вказує кількість подів, які мають бути запущені.

&nbsp;&nbsp;&nbsp;&nbsp;**selector**: Визначає мітки для вибору подів, яким буде застосовано цей Deployment.

&nbsp;&nbsp;&nbsp;&nbsp;**template**: Шаблон для створення подів із вказівкою контейнерів та їх конфігурації (наприклад, образ контейнера, порти).
</details>

[До змісту](#Зміст)

---

### Маніфест Service

```yaml
apiVersion: v1                # обов'язкове. Версія API для Service
kind: Service                 # обов'язкове. Тип ресурсу (Service — для надання доступу до подів через мережу)
metadata:                     # обов'язкове. Містить метадані об'єкта
  name: example-service       # обов'язкове. Унікальне ім'я об'єкта
spec:                         # обов'язкове. Специфікація для налаштування сервісу
  type: ClusterIP             # не обов'язкове. Тип сервісу (ClusterIP — доступ тільки всередині кластера)
  selector:                   # обов'язкове. Визначає мітки для вибору подів, до яких буде підключено цей сервіс
    app: my-app
  ports:
    - protocol: TCP           # обов'язкове. Протокол для підключення
      port: 80                # обов'язкове. Порт, на якому буде слухати сервіс
      targetPort: 80          # обов'язкове. Порт на контейнері, до якого буде перенаправлено трафік
```
<details>
<summary>Пояснення:</summary>  

**apiVersion**: Визначає версію API для Service ресурсу.

**kind**: Тип ресурсу — Service, який забезпечує доступ до подів через певний порт.

**metadata**: Містить метадані об'єкта, такі як ім'я Service.

**spec**: Специфікація для налаштування Service.

&nbsp;&nbsp;&nbsp;**type**: Тип Service — може бути ClusterIP, NodePort, LoadBalancer.

&nbsp;&nbsp;&nbsp;**selector**: Мітки для вибору подів, які будуть обслуговуватися цим Service.

&nbsp;&nbsp;&nbsp;**ports**: Список портів, через які трафік буде спрямований на поди.
  </details>
    
[До змісту](#Зміст)

---

### Маніфест ConfigMap

```yaml
apiVersion: v1                # обов'язкове. Версія API для ConfigMap
kind: ConfigMap               # обов'язкове. Тип ресурсу (ConfigMap — для зберігання конфігураційних даних)
metadata:                     # обов'язкове. Містить метадані об'єкта
  name: example-config        # обов'язкове. Унікальне ім'я об'єкта
data:                         # обов'язкове. Дані, які будуть збережені в ConfigMap
  APP_MODE: production        # не обов'язкове. Ключі й значення, які будуть доступні для контейнерів
  LOG_LEVEL: info
```
<details>
<summary>Пояснення:</summary>  
  
**apiVersion**: Визначає версію API для ConfigMap.

**kind**: Тип ресурсу — ConfigMap, що використовується для зберігання конфігураційних даних.

**metadata**: Містить метадані об'єкта.

**data**: Ключ-значення для зберігання конфігураційних параметрів.
</details>  

[До змісту](#Зміст)

---

### Маніфест Secret

```yaml
apiVersion: v1                # обов'язкове. Версія API для Secret
kind: Secret                  # обов'язкове. Тип ресурсу (Secret — для зберігання чутливих даних)
metadata:                     # обов'язкове. Містить метадані об'єкта
  name: example-secret        # обов'язкове. Унікальне ім'я об'єкта
type: Opaque                  # не обов'язкове. Тип секрету (Opaque — за замовчуванням, означає, що це загальні дані)
data:                         # обов'язкове. Дані, що містять закодовані значення
  DB_PASSWORD: cGFzc3dvcmQ=   # обов'язкове. Дані закодовані у форматі base64 (пароль до БД у цьому прикладі)
```
<details>
<summary>Пояснення:</summary>  

**apiVersion**: Визначає версію API для Secret.

**kind**: Тип ресурсу — Secret, який використовується для зберігання чутливих даних.

**metadata**: Містить метадані об'єкта.

**type**: Тип секрету — зазвичай Opaque, але можуть бути й інші.

**data**: Закодовані у base64 чутливі дані.
</details>  

[До змісту](#Зміст)

---

### Маніфест PersistentVolume

```yaml
apiVersion: v1                            # обов'язкове. Версія API для PersistentVolume
kind: PersistentVolume                    # обов'язкове. Тип ресурсу (PersistentVolume — для зберігання персистентних даних)
metadata:                                 # обов'язкове. Містить метадані об'єкта
  name: example-pv                        # обов'язкове. Унікальне ім'я об'єкта
spec:                                     # обов'язкове. Специфікація для налаштування PersistentVolume
  capacity:
    storage: 1Gi                          # обов'язкове. Обсяг пам'яті, доступний для цього PersistentVolume
  accessModes:
    - ReadWriteOnce                       # обов'язкове. Тип доступу до об'єкта (ReadWriteOnce — доступ з одного вузла)
  persistentVolumeReclaimPolicy: Retain   # обов'язкове. Політика утримання тома (Retain — залишити том після видалення PVC)
  storageClassName: manual                # не обов'язкове. Ім'я класу зберігання
  hostPath:
    path: /mnt/data                       # обов'язкове. Шлях до локальної директорії на хості
```
<details>
<summary>Пояснення:</summary>  

**apiVersion**: Версія API для PersistentVolume.

**kind**: Тип ресурсу — PersistentVolume, який надає фізичний об'єм для зберігання даних.

**metadata**: Містить метадані об'єкта.

**spec**: Специфікація для налаштування PersistentVolume:

&nbsp;&nbsp;&nbsp;**capacity**: Потужність PV, яка визначає обсяг доступного простору.

&nbsp;&nbsp;&nbsp;**accessModes**: Режими доступу до PV (наприклад, ReadWriteOnce).

&nbsp;&nbsp;&nbsp;**persistentVolumeReclaimPolicy**: Політика утилізації після звільнення PV.

&nbsp;&nbsp;&nbsp;**storageClassName**: Клас зберігання, який визначає способи управління зберіганням.

&nbsp;&nbsp;&nbsp;**hostPath**: Місце для зберігання даних на фізичному хості.
</details>  

[До змісту](#Зміст)

---

### Маніфест PersistentVolumeClaim

```yaml
apiVersion: v1                             # обов'язкове. API-версія для PVC
kind: PersistentVolumeClaim                # обов'язкове. Тип ресурсу — PVC
metadata:
  name: my-pvc                             # обов'язкове. Ім'я PVC (має збігатись з claimName у Deployment)
spec:
  accessModes:                             # обов'язкове. Режим доступу до тому
    - ReadWriteOnce                        # дозволяє одному поду одночасно монтувати том для читання і запису
  resources:                               # обов'язкове. Запитувані ресурси
    requests:
      storage: 1Gi                         # обсяг пам’яті, яку запитує PVC
  storageClassName: standard               # не обов'язкове, але часто потрібне. Вказує StorageClass, який буде використано
```
<details>
<summary>Пояснення:</summary>  
 
**apiVersion**: Версія API для PersistentVolumeClaim.

**kind**: Тип ресурсу — PersistentVolumeClaim, що використовується для запиту PersistentVolume.

**metadata**: Містить метадані об'єкта.

**spec**: Специфікація для налаштування PersistentVolumeClaim:

&nbsp;&nbsp;&nbsp;**accessModes**: Режими доступу до PVC.

&nbsp;&nbsp;&nbsp;**resources**: Ресурси, які запитуються для PVC, зокрема обсяг зберігання.

&nbsp;&nbsp;&nbsp;**storageClassName**: Клас зберігання для PVC.
</details>  

[До змісту](#Зміст)

---

### Маніфест StatefulSet

```yaml
apiVersion: apps/v1                 # обов'язкове. Версія API для StatefulSet
kind: StatefulSet                   # обов'язкове. Тип ресурсу (StatefulSet — для керування станом подів)
metadata:                           # обов'язкове. Містить метадані об'єкта
  name: example-statefulset         # обов'язкове. Унікальне ім'я об'єкта
spec:                               # обов'язкове. Специфікація для налаштування StatefulSet
  serviceName: example-headless     # обов'язкове. Ім'я сервісу, який керує доступом до подів
  replicas: 2                       # не обов'язкове. Кількість реплік для StatefulSet
  selector:                         # обов'язкове. Вибір подів для керування
    matchLabels:
      app: my-app
  template:                         # обов'язкове. Шаблон для створення подів
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-container
          image: redis
  volumeClaimTemplates:             # обов'язкове. Опис шаблону PVC для кожного поду
    - metadata:
        name: data                  # обов'язкове. Ім'я PVC
      spec:
        accessModes:
          - ReadWriteOnce           # обов'язкове. Тип доступу до об'єкта
        resources:
          requests:
            storage: 1Gi            # обов'язкове. Кількість запитуваного місця для зберігання
```
<details>
<summary>Пояснення:</summary>  
 
**apiVersion**: Версія API для StatefulSet.

**kind**: Тип ресурсу — StatefulSet, що використовується для керування подами з постійними іменами.

**metadata**: Містить метадані об'єкта.

**spec**: Специфікація для налаштування StatefulSet:

&nbsp;&nbsp;&nbsp;**serviceName**: Ім'я для пов'язаної служби.

&nbsp;&nbsp;&nbsp;**replicas**: Кількість реплік для створення.

&nbsp;&nbsp;&nbsp;**selector**: Мітки для вибору подів.

&nbsp;&nbsp;&nbsp;**template**: Шаблон для створення подів.

&nbsp;&nbsp;&nbsp;**volumeClaimTemplates**: Шаблони PVC для кожного поду.  
</details>  

[До змісту](#Зміст)

---

### Маніфест DaemonSet

```yaml
apiVersion: apps/v1                 # обов'язкове. Версія API для DaemonSet
kind: DaemonSet                     # обов'язкове. Тип ресурсу (DaemonSet — для запуску одного екземпляра пода на кожному вузлі в кластері)
metadata:                           # обов'язкове. Містить метадані об'єкта
  name: example-daemonset           # обов'язкове. Унікальне ім'я об'єкта
spec:                               # обов'язкове. Специфікація для налаштування DaemonSet
  selector:                         # обов'язкове. Визначає мітки для вибору подів, які відповідають шаблону DaemonSet
    matchLabels:                    # обов'язкове. Вибір подів на основі міток
      app: monitoring-agent
  template:                         # обов'язкове. Шаблон для створення подів
    metadata:                     
      labels:                       # обов'язкове. Мітки для подів, які будуть створені
        app: monitoring-agent
    spec:                           # обов'язкове. Специфікація для контейнерів у поді
      containers:                   # обов'язкове. Опис контейнерів у поді
        - name: agent               # обов'язкове. Ім'я контейнера
          image: busybox            # обов'язкове. Вказує образ контейнера для запуску
          command: ["sh", "-c", "while true; do echo hello; sleep 10; done"]  # не обов'язкове. Команда для виконання в контейнері (у даному випадку нескінченний цикл)
```
<details>
<summary>Пояснення:</summary>  

**apiVersion**: Визначає версію API для ресурсу. В даному випадку використовуємо версію apps/v1, оскільки DaemonSet відноситься до цієї групи API.

**kind**: Тип ресурсу — у даному випадку DaemonSet, що вказує на те, що цей об'єкт керує розгортанням одного екземпляра контейнера на кожному вузлі в кластері.

**metadata**: Містить метадані об'єкта, зокрема:

&nbsp;&nbsp;&nbsp;**name**: Унікальне ім'я ресурсу. У даному випадку це example-daemonset.

**spec**: Основна специфікація для налаштування DaemonSet:

&nbsp;&nbsp;&nbsp;**selector**: Визначає критерії для вибору подів, яким буде застосовано DaemonSet:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**matchLabels**: Вказує на мітки, які повинні збігатися для підбору подів.

&nbsp;&nbsp;&nbsp;**template**: Шаблон для створення подів:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**metadata**:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**labels**: Мітки, що додаються до кожного поду, який буде створений цим DaemonSet.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**spec**: Специфікація для контейнерів у поді:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**containers**: Список контейнерів, що мають бути запущені в поді.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**- name**: Ім'я контейнера.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**image**: Образ контейнера для запуску.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**command**: Опційний параметр, що вказує команду, яку контейнер має виконати після старту (в даному випадку нескінченний цикл).
</details>  

[До змісту](#Зміст)

---


