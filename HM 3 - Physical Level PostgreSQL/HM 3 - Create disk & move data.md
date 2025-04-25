# Отчет по миграции директории данных PostgreSQL

  

## Начальная проверка и подготовка

  

1.  **Проверка статуса кластера**

```bash

pg_ctlcluster 17 main status

```

**Скриншот 1**: Вывод команды pg_ctlcluster, показывающий текущий статус кластера

![image](https://github.com/user-attachments/assets/60eeec26-3305-4027-a2b3-5f55e4cf9329)


  

---

  

2.  **Создание тестовых данных**

```bash

# Подключение к PostgreSQL под пользователем postgres

sudo  -u  postgres  psql

  

# Создание тестовой таблицы и вставка данных

CREATE TABLE employees (
id SERIAL PRIMARY KEY,
first_name VARCHAR(50),
last_name VARCHAR(50),
department VARCHAR(50),
position VARCHAR(50),
salary NUMERIC(10, 2),
hire_date DATE
);

  
# Вставка данных в тестовую таблицу

INSERT INTO employees (first_name, last_name, department, position, salary, hire_date)

VALUES
('John', 'Doe', 'IT', 'Software Engineer', 75000.00, '2020-05-15'),
('Jane', 'Smith', 'HR', 'HR Manager', 65000.00, '2018-09-01'),
('Alice', 'Johnson', 'Marketing', 'Marketing Specialist', 55000.00, '2021-03-10'),
('Bob', 'Brown', 'IT', 'DevOps Engineer', 80000.00, '2019-07-22'),
('Charlie', 'Davis', 'Finance', 'Accountant', 60000.00, '2017-11-30'),
('Eve', 'Miller', 'Sales', 'Sales Representative', 50000.00, '2022-01-12'),
('Frank', 'Wilson', 'IT', 'Database Administrator', 70000.00, '2016-04-18');

```

**Скриншот 2**: Создание тестовой таблицы и вставка данных

![image](https://github.com/user-attachments/assets/9ad88c43-e09c-47f8-afc1-a3f4a4cc6bf7)



  

---

  

3.  **Остановка кластера PostgreSQL**

```bash

sudo  pg_ctlcluster  17  main  stop

```

**Скриншот 3**: Остановка кластера PostgreSQL

![image](https://github.com/user-attachments/assets/2ebad4a3-a19c-4a7d-bf05-03ba7dfbd3e0)


  

---

  

## Настройка хранилища

  

1.  **Создание нового диска**

- Создан новый диск размером 10GB в настройках ВМ

- Диск подключен к виртуальной машине через режим редактирования  

![image](https://github.com/user-attachments/assets/8b0a0e51-39d7-4680-9688-6978c02a9981)




  

---

  

2.  **Инициализация диска**

```bash

# Создание разделов на новом диске (/dev/sdb)

sudo  fdisk  /dev/sdb  

# Форматирование раздела

sudo  mkfs.ext4  /dev/sdb1  

# Создание точки монтирования

sudo  mkdir  /mnt/data  

# Монтирование файловой системы

sudo  mount  /dev/sdb1  /mnt/data

```

**Скриншот 5**: Процесс разметки и форматирования диска
![image](https://github.com/user-attachments/assets/694f4520-9403-4498-a19e-0c4bbb7c47d7)

  

---

  

3.  **Настройка постоянного монтирования**

```bash

# Добавление записи в /etc/fstab

/dev/sdb1  /mnt/data  ext4  defaults  0  0

```

**Скриншот 6**: Содержимое файла /etc/fstab

![image](https://github.com/user-attachments/assets/8d079076-eb3a-4518-940a-150db37a16d6)
  

---

  

4.  **Проверка монтирования после перезагрузки**

```bash

sudo  reboot

df  -h  # Проверка сохранения монтирования

```

![image](https://github.com/user-attachments/assets/38780743-b6f7-4119-a5ea-3a91f60187e9)
![image](https://github.com/user-attachments/assets/42549166-6e94-4eae-9134-ec6436febdf8)



  

---

  

## Миграция данных PostgreSQL

  

1.  **Установка владельца директории**

```bash

sudo  chown  -R  postgres:postgres  /mnt/data/

```

**Скриншот 8**: Результат выполнения команды ls -ld /mnt/data
![image](https://github.com/user-attachments/assets/d6154bc3-2409-445d-b42d-6efd17e06554)


  

---

  

2.  **Перемещение данных PostgreSQL**

```bash

sudo  mv  /var/lib/postgresql/17  /mnt/data

```

**Скриншот 9**: Процесс перемещения данных

![image](https://github.com/user-attachments/assets/f0668d1d-7432-4e8d-9301-34ce9384d5d4)
![image](https://github.com/user-attachments/assets/eede20c7-b058-495d-b436-bbffc08efc1f)
  

---

  

3.  **Первая попытка запуска кластера**

```bash

sudo  pg_ctlcluster  17  main  start

```

**Результат**: ❌ Неудача - PostgreSQL не может найти директорию с данными по новому пути

  

**Скриншот 10**: Сообщение об ошибке при попытке запуска

![image](https://github.com/user-attachments/assets/b1371d38-35a5-4a51-8e5e-18f3ea3410d8)


  

---

  

## Изменение конфигурации

  

1.  **Необходимые изменения**

- Файл для изменения: `/etc/postgresql/15/main/postgresql.conf`

- Параметр для изменения: `data_directory`

- Новое значение: `/mnt/data/17/main`

- Причина: PostgreSQL необходимо указать новое расположение директории с данными

  

![image](https://github.com/user-attachments/assets/e9d2e988-d2fc-4d33-95a1-f44adbabe83b)


  

---

  

2.  **Финальный запуск кластера**

```bash

sudo  pg_ctlcluster  17  main  start

```

**Результат**: ✅ Успех - кластер PostgreSQL запущен с новой директорией данных

  

![image](https://github.com/user-attachments/assets/f4f4e60f-3c8e-4b82-b96e-d06e0c31eef1)



  

---

  

## Проверка данных  


# Проверка данных в тестовой таблице

```
SELECT * FROM employees

```

![image](https://github.com/user-attachments/assets/be2fa661-44b8-4680-8371-1e61f947484e)


