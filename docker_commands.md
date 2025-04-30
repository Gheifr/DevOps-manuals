## Зміст:  
- [Робота з контейнерами](#Робота-з-контейнерами)  
- [Робота з образами](#Робота-з-образами)  
- [Інше](#Інше)  



## Робота з контейнерами  
```
docker run IMAGE_NAME               # Запуск контейнера
docker ps                           # Перегляд запущених контейнерів
docker ps -a                        # Перегляд усіх (навіть зупинених) контейнерів
docker stop CONTAINER_ID            # Зупинити контейнер
docker start CONTAINER_ID           # Запустити зупинений контейнер
docker restart CONTAINER_ID         # Перезапуск
docker rm CONTAINER_ID              # Видалити контейнер
docker logs CONTAINER_ID            # Перегляд логів
docker exec -it CONTAINER_ID bash   # Виконати команду в контейнері (bash)
```

## Робота з образами  
```
docker build -t NAME .              # Зібрати образ з Dockerfile
docker images                       # Список образів
docker rmi IMAGE_ID                 # Видалити образ
docker pull IMAGE_NAME              # Завантажити з Docker Hub
docker push USER/IMAGE_NAME         # Завантажити на Docker Hub
```

## Інше  
```
docker volume ls                    # Перегляд томів
docker network ls                   # Перегляд мереж
docker-compose up                   # Запуск docker-compose
docker-compose down                 # Зупинка та видалення ресурсів
```