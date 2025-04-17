
---
## 1. **Установка Jenkins**

**1.1 Установка Jenkins**  
Для Debian/Ubuntu выполните следующие команды:

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```

**1.2 Установка Java (OpenJDK 17)**
Убедитесь, что установлена версия Java 17:

```bash
sudo apt-get install openjdk-17-jdk
java -version
```

**1.3 Запуск и автозагрузка Jenkins**
Запустите службу Jenkins и добавьте её в автозагрузку:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

**1.4 Первичная настройка Jenkins**
Откройте браузер и перейдите по адресу `http://<IP_виртуальной_машины>:8080`.
Введите ключ из файла `/var/lib/jenkins/secrets/initialAdminPassword`, когда система его запросит.

---

## 2. **Настройка Jenkins**

**2.1 Добавление учетных данных**  
- Перейдите в **Manage Jenkins → Manage Credentials**.  
- Выберите раздел **Stores scoped to Jenkins → Global credentials**.  
- Нажмите **Add Credentials** и добавьте необходимые данные (SSH-ключи, пароли, токены).

**2.2 Настройка GitLab аутентификации**  
- Перейдите в **Manage Jenkins → Configure System**.  
- В разделе **GitLab** настройте подключение к GitLab, используя ранее добавленный токен.  
- Проверьте соединение с помощью кнопки **Test Connection**.

**2.3 Проверка установленных плагинов**  
Убедитесь, что следующие плагины установлены:  
- GitLab Plugin  
- Workspace Cleanup Plugin  
- Matrix Authorization Strategy Plugin  

**2.4 Настройка прав доступа для анонимных пользователей**  
- Перейдите в **Manage Jenkins → Configure Global Security**.  
- В разделе **Authorization** выберите стратегию **Project-based Matrix Authorization Strategy**.  
- Предоставьте все необходимые права анонимному пользователю для работы вебхуков.

---

## 3. **Создание и Настройка Пайплайнов**

**3.1 Создание нового пайплайна**  
- Откройте **Dashboard → New Item**.  
- Введите имя проекта и выберите тип **Pipeline**.

**3.2 Настройка этапов пайплайна**  
Определите следующие основные этапы:  
- **Checkout**: Клонирование репозитория с указанием ветки и учетных данных.  
- **Restore & Build**: Выполнение `.NET Restore` и сборка приложения.  
- **Tests**: Запуск тестов.  
- **Docker Image Build**: Сборка Docker образа.  
- **Publish**: Публикация образа в реестре.  
- **Deployment**: Удаление старого контейнера и запуск нового.

---

## 4. **Настройка Проекта DockerCompose**

**4.1 Конфигурация DockerCompose**  
- Склонируйте проект DockerCompose в корневую директорию виртуальной машины.  
- Создайте папку `ssl` с сертификатами и установите права на чтение:

  ```bash
  chmod 400 /DockerCompose/production/ssl/commhub.key
  chmod 400 /DockerCompose/production/ssl/commhub.pem
  ```

- Добавьте пользователя Jenkins в группу Docker:

  ```bash
  sudo usermod -aG docker jenkins
  sudo systemctl restart jenkins
  ```

**4.2 Настройка healthchecks**\
Установите права на выполнение скриптов healthcheck:

```bash
chmod +x /DockerCompose/production/configuration/{container}/healthcheck.sh
```

**4.3 Создание keyfile для MongoDB**\
Сгенерируйте ключ для MongoDB:

```bash
mkdir -p /DockerCompose/production/configuration/mongo
cd /DockerCompose/production/configuration/mongo
openssl rand -base64 756 > keyfile.key
chmod 400 keyfile.key
chown 999:999 keyfile.key
```

---

## 5. **Настройка GitLab Webhooks**

**5.1 Добавление вебхука в GitLab**

- Зайдите в настройки вашего проекта в GitLab.
- Перейдите в раздел **Webhooks** и нажмите "Add webhook".
- Пропишите URL Jenkins (например, `http://<Jenkins_URL>/job/<Pipeline_Name>`).
- Укажите триггер (`Push events`) и фильтр для нужной ветки.
- При необходимости отключите верификацию SSL.
- Сохраните изменения.


---

## 6. **Ограничение Одновременного Выполнения Пайплайнов**

**6.1 Настройка количества исполнителей**

- Перейдите в раздел **Nodes**.
- Выберите мастер-ноду и нажмите "Configure".
- Установите количество процессов исполнителей (`Executors`) равным 1.
- Сохраните настройки.

---