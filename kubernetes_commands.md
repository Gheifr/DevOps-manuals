## Зміст:  
-[Інформація про ресурси](#Інформація-про-ресурси)  
-[Робота з ресурсами](#Робота-з-ресурсами)  
-[Інше](#Інше)  



##  Інформація про ресурси  
```
kubectl get pods                  # Список подів
kubectl get services              # Список сервісів
kubectl get deployments           # Список деплойментів
kubectl describe pod POD_NAME     # Деталі про под
kubectl logs POD_NAME             # Логи з пода
kubectl get nodes                 # Ноди в кластері
```

## Робота з ресурсами  
```
kubectl apply -f file.yaml                  # Створити або оновити ресурс
kubectl create -f file.yaml                 # Створити ресурс
kubectl delete -f file.yaml                 # Видалити ресурс
kubectl delete pod POD_NAME                 # Видалити конкретний под
kubectl exec -it POD_NAME -- bash           # Увійти в под
kubectl scale deployment NAME --replicas=3  # Змінити кількість реплік
```

## Інше  
```
kubectl config get-contexts       # Перегляд доступних контекстів
kubectl config use-context NAME   # Перемикання контексту
kubectl apply -k ./folder         # Kustomize: застосувати каталог
kubectl port-forward POD 8080:80  # Проксі портів [LOCAL_PORT:]REMOTE_PORT
```