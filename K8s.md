## K8s Manifest Structure
### Зміст  
- [apiVersion & kind](#apiVersion-and-kind)  
- [Маніфест: Deployment](#Маніфест-Deployment)  
- [Маніфест: Service](#Маніфест-Service)  
- [Маніфест: ConfigMap](#Маніфест-ConfigMap)  
- [Маніфест: Secret](#Маніфест-Secret) 
- [Маніфест: PersistentVolume (PV)](#Маніфест-PersistentVolume-(PV))  
- [Маніфест: PersistentVolumeClaim (PVC)](#Маніфест-PersistentVolumeClaim-(PVC))  
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


|[Догори](#Зміст)  

---


### Маніфест Deployment

```yaml
apiVersion: apps/v1               # обов'язкове. Визначає версію API для об'єкта (у даному випадку для Deployment)
kind: Deployment                  # обов'язкове. Тип ресурсу (Deployment — для управління наборами реплік контейнерів)
metadata:                         # обов'язкове. Містить метадані об'єкта
  name: example-deployment        # обов'язкове. Унікальне ім'я об'єкта в кластері
spec:                             # обов'язкове. Специфікація для налаштування об'єкта
  replicas: 3                     # не обов'язкове. Кількість реплік контейнера, що має бути запущено
  selector:                       # обов'язкове. Визначає умови для вибору подів, які відповідають шаблону
    matchLabels:                  # обов'язкове. Вибір за мітками
      app: my-app
  template:                       # обов'язкове. Шаблон для створення подів
    metadata:
      labels:                     # обов'язкове. Мітки, що додаються до кожного поду
        app: my-app
    spec:                         # обов'язкове. Специфікація для контейнерів у поді
      containers:
        - name: my-container      # обов'язкове. Ім'я контейнера
          image: nginx:1.25       # обов'язкове. Вказує образ контейнера для запуску
          ports:
            - containerPort: 80   # не обов'язкове. Вказує порт контейнера
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

|[Догори](#Зміст)

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
    
|[Догори](#Зміст)

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

|[Догори](#Зміст)

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

|[Догори](#Зміст)

---

### Маніфест PersistentVolume (PV)

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

|[Догори](#Зміст)

---

### Маніфест PersistentVolumeClaim (PVC)

```yaml
apiVersion: v1                # обов'язкове. Версія API для PersistentVolumeClaim
kind: PersistentVolumeClaim   # обов'язкове. Тип ресурсу (PersistentVolumeClaim — для запиту до PersistentVolume)
metadata:                     # обов'язкове. Містить метадані об'єкта
  name: example-pvc           # обов'язкове. Унікальне ім'я об'єкта
spec:                         # обов'язкове. Специфікація для налаштування PersistentVolumeClaim
  accessModes:
    - ReadWriteOnce           # обов'язкове. Тип доступу до об'єкта (ReadWriteOnce — доступ з одного вузла)
  resources:
    requests:
      storage: 1Gi            # обов'язкове. Кількість запитуваного місця для зберігання
  storageClassName: manual    # не обов'язкове. Ім'я класу зберігання, який буде використано
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

|[Догори](#Зміст)

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

|[Догори](#Зміст)

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

|[Догори](#Зміст)

---


