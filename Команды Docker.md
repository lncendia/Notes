
---

## 1. **Сборка образа**
Создание Docker-образа из `Dockerfile` в текущей директории:
```bash
docker build -t image-name:tag .
```
- `-t image-name:tag`: Указание имени и тега образа.
- `.`: Путь к директории с `Dockerfile`.

Сборка образа с использованием конкретного файла (например, `Dockerfile.dev`):
```bash
docker build -t image-name:tag -f Dockerfile.dev .
```

---

## 2. **Управление контейнерами**

### **Просмотр контейнеров**
Показать запущенные контейнеры:
```bash
docker ps
```
Показать все контейнеры (включая остановленные):
```bash
docker ps -a
```

### **Запуск контейнера**
Запустить контейнер в фоновом режиме:
```bash
docker run -d image-name
```
Запустить контейнер с пробросом портов:
```bash
docker run -p host-port:container-port image-name
```

### **Остановка/Перезапуск контейнера**
Остановить контейнер:
```bash
docker stop container-id-or-name
```
Перезапустить контейнер:
```bash
docker restart container-id-or-name
```

### **Подключение к контейнеру**
Подключиться к запущенному контейнеру через интерактивную оболочку:
```bash
docker exec -it container-id-or-name /bin/bash
```

---

## 3. **Логи контейнера**

### **Просмотр логов**
Показать логи контейнера:
```bash
docker logs container-id-or-name
```
Показать логи в реальном времени:
```bash
docker logs -f container-id-or-name
```

### **Очистка логов**
Очистить файл логов контейнера:
```bash
truncate -s 0 $(docker inspect --format='{{.LogPath}}' container-id-or-name)
```

---

## 4. **Удаление контейнеров и образов**

### **Удаление контейнеров**
Удалить остановленный контейнер:
```bash
docker rm container-id-or-name
```
Удалить контейнер, даже если он запущен:
```bash
docker rm -f container-id-or-name
```
Удалить все остановленные контейнеры:
```bash
docker container prune
```

### **Удаление образов**
Удалить Docker-образ:
```bash
docker rmi image-id-or-name
```
Удалить все неиспользуемые образы:
```bash
docker image prune -a
```

---

## 5. **Информация о контейнерах и системе**

### **Инспект контейнера**
Показать подробную информацию о контейнере:
```bash
docker inspect container-id-or-name
```
Получить конкретное поле (например, IP-адрес):
```bash
docker inspect -f '{{.NetworkSettings.IPAddress}}' container-id-or-name
```

### **Использование дискового пространства**
Показать использование диска Docker:
```bash
docker system df
```

---

## 6. **Очистка системы**

### **Очистка билд-кеша**
Удалить неиспользуемые билд-кеши:
```bash
docker builder prune
```
Удалить все билд-кеши (включая используемые):
```bash
docker builder prune --all
```

### **Полная очистка системы**
Удалить все неиспользуемые данные (контейнеры, образы, сети, кеши):
```bash
docker system prune -a
```

---

## 7. **Дополнительные команды**

### **Просмотр списка образов**
Показать все Docker-образы:
```bash
docker images
```

### **Создание сети**
Создать пользовательскую сеть:
```bash
docker network create network-name
```

### **Подключение к сети**
Подключить контейнер к сети:
```bash
docker network connect network-name container-id-or-name
```