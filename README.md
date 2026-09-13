# Инструкция по развертыванию виртуального сервера

## Содержание

- [1. Установка Linux сервера](#1-установка-linux-сервера)
  - [1.1. Установка Oracle Virtual Box](#11-установка-oracle-virtual-box)
  - [1.2. Установка VirtualBox Extension Pack](#12-установка-virtualbox-extension-pack)
  - [1.3. Создание виртуальной машины и установка Ubuntu Server](#13-создание-виртуальной-машины-и-установка-ubuntu-server)
  - [1.4. Процесс установки Ubuntu Server](#14-процесс-установки-ubuntu-server)
  - [1.5. Первоначальная настройка после перезагрузки](#15-первоначальная-настройка-после-перезагрузки)
  - [1.6. Проверка и установка SSH (при необходимости)](#16-проверка-и-установка-ssh-при-необходимости)
  - [1.7. Настройка сетевого моста](#17-настройка-сетевого-моста)
  - [1.8. Управление Virtual Box из командной строки (VBoxManage)](#18-управление-virtual-box-из-командной-строки-vboxmanage)
  - [1.9. Подключение к серверу по SSH](#19-подключение-к-серверу-по-ssh)
  - [1.10. Настройка SSH-ключей (без пароля)](#110-настройка-ssh-ключей-без-пароля)
  - [1.11. Проверка доступа в интернет](#111-проверка-доступа-в-интернет)
- [2. Запуск приложения](#2-запуск-приложения)
  - [2.1. Установка JDK](#21-установка-jdk)
  - [2.2. Сборка и запуск приложения (JAR)](#22-сборка-и-запуск-приложения-jar)
  - [2.3. Проверка работы на порту 8000](#23-проверка-работы-на-порту-8000)
  - [2.4. Копирование JAR-файла на сервер](#24-копирование-jar-файла-на-сервер)
  - [2.5. Запуск в фоновом режиме с логированием](#25-запуск-в-фоновом-режиме-с-логированием)
  - [2.6. Проверка запущенных процессов и портов](#26-проверка-запущенных-процессов-и-портов)
  - [2.7. Проверка в браузере](#27-проверка-в-браузере)
- [3. Контейнер сервлетов (Tomcat)](#3-контейнер-сервлетов-tomcat)
  - [3.1. Подготовка](#31-подготовка)
  - [3.2. Установка Tomcat 9](#32-установка-tomcat-9)
  - [3.3. Создание systemd-сервиса](#33-создание-systemd-сервиса)
  - [3.4. Запуск Tomcat](#34-запуск-tomcat)
  - [3.5. Настройка firewall (при необходимости)](#35-настройка-firewall-при-необходимости)
  - [3.6. Настройка пользователей для веб-интерфейса](#36-настройка-пользователей-для-веб-интерфейса)
  - [3.7. Доступ к Manager и Host Manager (при необходимости)](#37-доступ-к-manager-и-host-manager-при-необходимости)
  - [3.8. Перезапуск после изменений](#38-перезапуск-после-изменений)
  - [3.9. Проверка и деплой WAR](#39-проверка-и-деплой-war)
- [4. Проксирование с помощью nginx](#4-проксирование-с-помощью-nginx)
  - [4.1. Привязка имени к IP (локально)](#41-привязка-имени-к-ip-локально)
  - [4.2. Установка nginx на сервер](#42-установка-nginx-на-сервер)
  - [4.3. Создание конфигурации сайта](#43-создание-конфигурации-сайта)
  - [4.4. Активация сайта](#44-активация-сайта)
  - [4.5. Проверка проксирования](#45-проверка-проксирования)

---

## 1. Установка Linux сервера

### 1.1. Установка Oracle Virtual Box

1. Перейдите по ссылке: <https://www.virtualbox.org/wiki/Downloads>
2. Выберите **VirtualBox Platform Packages** для Windows host.
3. Установите программу, выбрав подходящую папку и компоненты (можно оставить настройки по умолчанию).
4. Откройте установленное приложение Virtual Box.

### 1.2. Установка VirtualBox Extension Pack

1. На той же странице загрузите **VirtualBox Extension Pack**, приняв лицензионное соглашение.
2. В VirtualBox Manager откройте: **Файл → Плагины**.
3. Нажмите на плюсик, выберите скачанный файл `.vbox-extpack` и установите плагин.

### 1.3. Создание виртуальной машины и установка Ubuntu Server

1. Скачайте Ubuntu Server по ссылке: <https://ubuntu.com/download/server> (ISO-файл).
2. В VirtualBox Manager нажмите **«Создать»**.
3. В разделе **«Имя и операционная система»**:
   - Имя: `Ubuntu Server`
   - Версия: `24.04`
   - Образ ISO: выберите скачанный ISO-файл (через «Другой...»).
   - Снимите галочку **«Осуществить установку автоматически»**.
4. В разделе **«Укажите виртуальное оборудование»**:
   - ОЗУ: `2048 МБ` (по умолчанию)
   - Процессоры: `1 ядро` (по умолчанию)
5. В разделе **«Укажите виртуальный жёсткий диск»**:
   - Размер: `25 ГБ` (по умолчанию)
   - Не ставьте галочку **«Выделить место в полном размере»** (динамический диск).
6. Нажмите **«Готово»** и дождитесь создания носителя.

### 1.4. Процесс установки Ubuntu Server

1. Запустите виртуальную машину и следуйте инструкциям установщика (взаимодействие только с клавиатурой):
   - Выберите язык.
   - Настройте раскладку клавиатуры.
   - **Type of Installation**: выберите вариант по умолчанию. **Search for third-party drivers**: отключите.
   - **Network Configuration**: оставьте по умолчанию.
   - **Proxy Configuration**: пропустите.
   - **Ubuntu Archive Mirror Configuration**: оставьте по умолчанию.
   - **Guided Storage Configuration**: выберите **«Использовать весь диск»**, включите **LVM**.
   - **Storage Configuration**: проверьте и подтвердите (**Продолжить**).
   - **Profile Configuration**: введите ваше имя, имя сервера, имя пользователя и пароль.
   - **Upgrade to Ubuntu Pro**: пропустите.
   - **SSH Configuration**: обязательно выберите установку **OpenSSH server**. Импорт ключей пропустите.
   - **Featured Server Snaps**: ничего не выбирайте.
2. По завершении установки выберите **«Перезагрузить сейчас»**.
3. При запросе удалите установочный носитель:
   - Через меню: **Устройства → Оптические диски → Извлечь диск**.
   - Если опция неактивна, просто нажмите **Enter**.

### 1.5. Первоначальная настройка после перезагрузки

```bash
sudo apt update
sudo apt upgrade
```

### 1.6. Проверка и установка SSH (при необходимости)

```bash
# Проверка
ssh

# Если команда не найдена
sudo apt-get install openssh-server
```

### 1.7. Настройка сетевого моста

1. Выключите виртуальную машину: `sudo poweroff` или **Machine → ACPI Shutdown**.
2. В VirtualBox Manager: **Настроить → Сеть**.
3. **Адаптер 1** → измените **NAT** на **Сетевой мост**.
4. Запустите машину и выполните:

```bash
ip address
```

5. Запишите IP-адрес (например, `192.168.1.102`).

### 1.8. Управление Virtual Box из командной строки (VBoxManage)

```bash
# Запуск в фоновом режиме
VBoxManage startvm "Ubuntu Server 24.04" --type headless

# Принудительное выключение
VBoxManage controlvm "Ubuntu Server 24.04" poweroff
```

### 1.9. Подключение к серверу по SSH

**Из Linux/macOS:**

```bash
ssh wil@192.168.1.102
```

**Из Windows (PuTTY):**

- В поле **«Имя хоста»** введите IP-адрес.
- Нажмите **«Открыть»**.

### 1.10. Настройка SSH-ключей (без пароля)

В **PowerShell** (от имени пользователя):

```powershell
ssh-keygen -t ed25519
# На все вопросы нажимайте Enter

type %USERPROFILE%\.ssh\id_ed25519.pub | ssh wil@192.168.1.102 "cat >> ~/.ssh/authorized_keys"
```

**Проверка:**

```powershell
ssh wil@192.168.1.102
```

### 1.11. Проверка доступа в интернет

```bash
wget https://github.com/Knyaz-v/java-module-3/blob/master/src/main/webapp/index.jsp
ls -la
rm index.jsp
```

---

## 2. Запуск приложения

### 2.1. Установка JDK

```bash
sudo apt-get install default-jdk
java -version
```

### 2.2. Сборка и запуск приложения (JAR)

1. Соберите приложение из модуля 2 в JAR-файл.
2. Запустите на сервере:

```bash
java -jar demo-0.0.1-SNAPSHOT.jar
```

### 2.3. Проверка работы на порту 8000

```bash
curl http://localhost:8000
```

### 2.4. Копирование JAR-файла на сервер

```bash
scp demo-0.0.1-SNAPSHOT.jar wil@192.168.1.102:~/projects/demo.jar
```

### 2.5. Запуск в фоновом режиме с логированием

```bash
nohup java -jar filename.jar >> app1.log &

# Или с указанием порта
java -jar demo.jar --server.port=8000 &
```

### 2.6. Проверка запущенных процессов и портов

```bash
less app1.log          # просмотр лога
ps af                  # список процессов
netstat -tnpl          # открытые порты
```

### 2.7. Проверка в браузере

Откройте браузер и перейдите по адресу:

```text
http://192.168.1.102:8000/
```

---

## 3. Контейнер сервлетов (Tomcat)

### 3.1. Подготовка

1. Соберите приложение из модуля 3 в WAR-файл.
2. Создайте системного пользователя для Tomcat:

```bash
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
```

### 3.2. Установка Tomcat 9

```bash
# Загрузка
wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.117/bin/apache-tomcat-9.0.117.tar.gz -P /tmp

# Распаковка
sudo tar xf /tmp/apache-tomcat-9*.tar.gz -C /opt/tomcat

# Символическая ссылка
sudo ln -s /opt/tomcat/apache-tomcat-9.0.117 /opt/tomcat/latest

# Права доступа
sudo chown -RH tomcat: /opt/tomcat/latest
sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
```

### 3.3. Создание systemd-сервиса

Создайте файл `/etc/systemd/system/tomcat.service`:

```ini
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/java-21-openjdk-amd64"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/opt/tomcat/latest"
Environment="CATALINA_HOME=/opt/tomcat/latest"
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/latest/bin/startup.sh
ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

> **Примечание:** Узнать путь к `JAVA_HOME` можно командой `readlink -f $(which java)`.

### 3.4. Запуск Tomcat

```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl status tomcat
sudo systemctl enable tomcat
```

### 3.5. Настройка firewall (при необходимости)

```bash
sudo ufw allow 8080/tcp
```

### 3.6. Настройка пользователей для веб-интерфейса

Отредактируйте `/opt/tomcat/latest/conf/tomcat-users.xml`:

```xml
<tomcat-users>
    <role rolename="admin-gui"/>
    <role rolename="manager-gui"/>
    <user username="admin" password="admin_password" roles="admin-gui,manager-gui"/>
</tomcat-users>
```

### 3.7. Доступ к Manager и Host Manager (при необходимости)

Снимите ограничение по IP, отредактировав:

- `/opt/tomcat/latest/webapps/manager/META-INF/context.xml`
- `/opt/tomcat/latest/webapps/host-manager/META-INF/context.xml`

Закомментируйте блок `RemoteAddrValve` или добавьте ваш IP.

### 3.8. Перезапуск после изменений

```bash
sudo systemctl restart tomcat
```

### 3.9. Проверка и деплой WAR

- Откройте браузер: `http://<IP>:8080`
- Manager App: `http://<IP>:8080/manager/html`
- В разделе **«WAR file to deploy»** загрузите ваш WAR-файл.

---

## 4. Проксирование с помощью nginx

### 4.1. Привязка имени к IP (локально)

На вашем локальном компьютере (Windows):

1. Откройте от имени администратора файл:

```text
c:\windows\system32\drivers\etc\hosts
```

2. Добавьте строку:

```text
192.168.1.102 example.com
```

(замените IP на адрес вашей виртуальной машины)

### 4.2. Установка nginx на сервер

```bash
sudo apt install nginx -y
```

### 4.3. Создание конфигурации сайта

Файл: `/etc/nginx/sites-available/example.com`

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8080/;
    }

    location /tomcat {
        proxy_pass http://127.0.0.1:8080/;
    }

    location /spring/ {
        proxy_pass http://127.0.0.1:8000/;
    }
}
```

### 4.4. Активация сайта

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
sudo nginx -t                 # проверка конфигурации
sudo systemctl start nginx
sudo systemctl enable nginx
sudo systemctl status nginx
sudo systemctl reload nginx   # при необходимости
```

### 4.5. Проверка проксирования

Откройте браузер на локальной машине и проверьте:

- `http://example.com` → Tomcat (порт 8080)
- `http://example.com/tomcat` → Tomcat
- `http://example.com/spring/` → Spring-приложение (порт 8000)
