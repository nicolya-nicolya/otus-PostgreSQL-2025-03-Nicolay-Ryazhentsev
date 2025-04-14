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

![image](https://github.com/user-attachments/assets/227edc43-e4c5-4a1e-9fcb-c3bed9720346)


> **Важно**: Параметр `-k` для `initdb` включает проверку контрольных сумм в базах данных





## 2. Установка PostgreSQL 17

### 2.1 Основная установка
```bash
apt install postgresql-17
```

### 2.2 Проверка установки
```bash
systemctl status postgresql
```


![image](https://github.com/user-attachments/assets/a28dc9a3-ad1c-4b37-a787-5056f551b576)


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

![image](https://github.com/user-attachments/assets/fd3b93b6-5e9a-435e-972c-d0def19d92f5)



## 4. Настройка после установки

### 4.1 Настройка доступа
1. Настройка удаленного доступа в `pg_hba.conf`


![image](https://github.com/user-attachments/assets/5a8e9c4f-9529-4c20-845a-308f6d4bf811)

> **Примечание**: Запись `0.0.0.0/0` означает "все возможные IPv4 адреса".
 
2. Установка пароля для пользователя postgres через psql

![image](https://github.com/user-attachments/assets/0e3d4c42-c486-4dd6-a10b-a228a9fc7ce6)


> **Примечание**: Для смены пароля от пользователя postgres необходимо подключиться из-под пользователя postgres и через консоль psql изменить пароль командой **\password**.

### 4.2 Оптимизация параметров
- Настройка основных параметров в `postgresql.conf` с помощью PGTune
- Настроить прослушивание в `postgresql.conf`:     
     **listen_addresses = '*'  **

> **Примечание**: Слушать подключения со всех адресов. '*' означает все доступные IP-адреса, также можно указать список конкретных адресов.


![image](https://github.com/user-attachments/assets/26469069-4edd-4616-8ea8-222110bec063)


## 5. Создание дополнительных экземпляров

### 5.1 Проверка существующих экземпляров
```bash
pg_lsclusters
```

![image](https://github.com/user-attachments/assets/2723a9d3-900f-48f6-bdd4-f398653eb964)


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

### 6.1 Проверка подключения
- Проверка через pgAdmin

  > **Примечание**: ip \a покажет адреса сетевых интерфейсов. В данном случае адрес: **192.168.118.128**


![image](https://github.com/user-attachments/assets/36c1bcae-e675-4e6f-9cb1-a80e0e107b42)
![image](https://github.com/user-attachments/assets/f25ff8bf-a967-4816-867b-2129abfc5b87)


- Проверка через DBeaver


![image](https://github.com/user-attachments/assets/b34455ec-fd84-4d8f-9e32-6c69ff2cfe87)


