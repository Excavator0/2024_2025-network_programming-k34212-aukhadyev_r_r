University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34212  
Author: Aukhadyev Ruslan Radikovich   
Lab: Lab3    
Date of create: 17.03.2025  
Date of finished: 17.03.2025  

## Отчёт о Лабораторной работе №3 <br>"Развертывание Netbox, сеть связи как источник правды в системе технического учета Netbox"

### Описание работы

В данной лабораторной работе вы ознакомитесь с интеграцией Ansible и Netbox и изучите методы сбора информации с помощью данной интеграции.


### Цель работы

С помощью Ansible и Netbox собрать всю возможную информацию об устройствах и сохранить их в отдельном файле.


### Ход работы

Для установки Netbox нам понадобятся postgres и redis. После установки postgres, создадим базу данных


Далее установим redis и склонируем репозиторий netbox


Создадим виртуальное окружение и загрузим необходимые библиотеки. Сгенерируем секретный ключ



Скопируем файл конфигурации netbox и заполним его

```
ALLOWED_HOSTS = ['localhost']
DATABASE = {
    'ENGINE': 'django.db.backends.postgresql',  # Database engine
    'NAME': 'netbox',         # Database name
    'USER': 'netbox',               # PostgreSQL username
    'PASSWORD': 'pass',           # PostgreSQL password
    'HOST': 'localhost',      # Database server
    'PORT': '',               # Database port (leave blank for default)
    'CONN_MAX_AGE': 300,      # Max database connection age
}
REDIS = {
    'tasks': {
        'HOST': 'localhost',
        'PORT': 6379,
        'USERNAME': '',
        'PASSWORD': '',
        'DATABASE': 0,
        'SSL': False,
    },
    'caching': {
        'HOST': 'localhost',
        'PORT': 6379,
        'USERNAME': '',
        'PASSWORD': '',
        'DATABASE': 1,
        'SSL': False,
    }
}
SECRET_KEY = 'qVBOXbN-(+f*#h%A_uO_VKwWfvr=IIr1W(+I%Ik=dO2D6JW$jv'
```

Выполняем миграции командой python3 manage.py migrate


Создаем суперпользователя


Запускаем с помощью команды python3 manage.py runserver 0.0.0.0:8000 и видим следующее


Выполняем python3 manage.py collectstatic и запускаем еще раз


Входим с данными суперпользователя


