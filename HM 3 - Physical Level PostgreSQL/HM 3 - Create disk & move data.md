# Отчет по миграции директории данных PostgreSQL

  

## Начальная проверка и подготовка

  

1.  **Проверка статуса кластера**

```bash

sudo  -u  postgres  pg_lsclusters

```

**Скриншот 1**: Вывод команды pg_lsclusters, показывающий текущий статус кластера

  

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


  

---

  

3.  **Остановка кластера PostgreSQL**

```bash

sudo  -u  postgres  pg_ctlcluster  17  main  stop

```

**Скриншот 3**: Остановка кластера PostgreSQL

  

---

  

## Настройка хранилища

  

1.  **Создание нового диска**

- Создан новый диск размером 10GB в настройках ВМ

- Диск подключен к виртуальной машине через режим редактирования

  

**Скриншот 4**: Настройки виртуальной машины с новым диском

  

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

  

---

  

3.  **Настройка постоянного монтирования**

```bash

# Добавление записи в /etc/fstab

/dev/sdb1  /mnt/data  ext4  defaults  0  0

```

**Скриншот 6**: Содержимое файла /etc/fstab

  

---

  

4.  **Проверка монтирования после перезагрузки**

```bash

sudo  reboot

df  -h  /mnt/data  # Проверка сохранения монтирования

```

**Скриншот 7**: Вывод команды df, показывающий примонтированный диск

  

---

  

## Миграция данных PostgreSQL

  

1.  **Установка владельца директории**

```bash

sudo  chown  -R  postgres:postgres  /mnt/data/

```

**Скриншот 8**: Результат выполнения команды ls -la /mnt/data

  

---

  

2.  **Перемещение данных PostgreSQL**

```bash

sudo  mv  /var/lib/postgresql/17  /mnt/data

```

**Скриншот 9**: Процесс перемещения данных

  

---

  

3.  **Первая попытка запуска кластера**

```bash

sudo  -u  postgres  pg_ctlcluster  17  main  start

```

**Результат**: ❌ Неудача - PostgreSQL не может найти директорию с данными по новому пути

  

**Скриншот 10**: Сообщение об ошибке при попытке запуска

  

---

  

## Изменение конфигурации

  

1.  **Необходимые изменения**

- Файл для изменения: `/etc/postgresql/15/main/postgresql.conf`

- Параметр для изменения: `data_directory`

- Новое значение: `/mnt/data/17/main`

- Причина: PostgreSQL необходимо указать новое расположение директории с данными

  

**Скриншот 11**: Измененная конфигурация в postgresql.conf

  

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

SELECT * FROM employees

```

![image](https://github.com/user-attachments/assets/7459b1a4-d6c6-4cb3-907a-8147ba0b499f)

