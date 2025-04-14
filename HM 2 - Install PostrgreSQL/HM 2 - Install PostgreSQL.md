# Отчет по установке PostgreSQL 17 под Linux

## 1. Подготовка к установке

### 1.1 Регистрация репозитория
```bash
sudo apt install -y postgresql-common
sudo /usr/share/postgresql-common/pgdg/apt.postgresql.org.sh
```

<!--
![Регистрация репозитория](screenshots/01_repo_registration.png)
-->

### 1.2 Настройка параметров установки
Файл конфигурации: `/etc/postgresql-common/createcluster.conf`

> **Важно**: Параметр `-k` для `initdb` включает проверку контрольных сумм в базах данных


![image](https://github.com/user-attachments/assets/a28dc9a3-ad1c-4b37-a787-5056f551b576)



## 2. Установка PostgreSQL 17

### 2.1 Основная установка
```bash
apt install postgresql-17
```

### 2.2 Проверка установки
```bash
systemctl status postgresql
```

<!--
![Проверка установки](screenshots/03_installation_check.png)
-->

## 3. Структура каталогов PostgreSQL

### 3.1 Основные каталоги
- **Конфигурация**: `/etc/postgresql/17/main`
- **Данные**: `/var/lib/postgresql/17/main`
- **Логи**: `/var/log/postgresql`
- **Исполняемые файлы**: `/usr/lib/postgresql/17/bin`

### 3.2 Проверка процессов 
```bash
ps auxf | grep postgres
```

<!--
![Структура каталогов](screenshots/04_directory_structure.png)
-->

## 4. Настройка после установки

### 4.1 Настройка доступа
1. Настройка удаленного доступа в `pg_hba.conf`
2. Установка пароля для пользователя postgres через psql

### 4.2 Оптимизация параметров
- Настройка основных параметров в `postgresql.conf` с помощью PGTune
- Настройка архивирования WAL
- Настройка системы ELK для архива логов

<!--
![Настройка доступа](screenshots/05_access_configuration.png)
-->

## 5. Создание дополнительных экземпляров

### 5.1 Проверка существующих экземпляров
```bash
pg_lsclusters
```

### 5.2 Создание нового экземпляра
```bash
pg_createcluster 17 instance02
sudo systemctl daemon-reload
pg_ctlcluster 17 instance02 start
```

> **Примечание**: Автозапуск, порт и каталоги настраиваются автоматически

<!--
![Создание экземпляра](screenshots/06_instance_creation.png)
-->

## 6. Проверка работоспособности

### 6.1 Проверка процессов
```bash
ps auxf | grep postgres
```

### 6.2 Проверка подключения
- Проверка через pgAdmin
- Проверка через DBeaver

<!--
![Проверка подключения](screenshots/07_connection_check.png)
-->

