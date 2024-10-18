University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34212  
Author: Aukhadyev Ruslan Radikovich  
Lab: Lab1  
Date of create: 30.09.2023  
Date of finished:  

## Лабораторная работа №1 "Установка CHR и Ansible, настройка VPN"
### Цель работы
Целью данной работы является развертывание виртуальной машины на базе любого облачного провайдера с установленной системой контроля конфигураций Ansible и установка CHR в VirtualBox.

### Ход работы
Для начала был приобретен сервер на платформе Yandex Cloud и сконфигурирована виртуальная машина
<p align="center"><img src="https://github.com/user-attachments/assets/6a52f695-3cbf-46c5-9777-fbb78e914cd8" width=700></p>

Далее было совершено подключение по ssh и установлены python3 и ansible
<p align="center"><img src="https://github.com/user-attachments/assets/843fb58b-66ff-40df-9c98-7bdd1cad4909" width=700></p>
<p align="center"><img src="https://github.com/user-attachments/assets/b9c4e65b-f4bc-41c2-a5e2-1ef4430d126b" width=700></p>

Установим CHR (Cloud Hosted Router) с помощью VirtualBox
<p align="center"><img src="https://github.com/user-attachments/assets/da27dbb2-d1a6-48b2-874b-3d17fcfc9ac1" width=700></p>

Теперь настроим виртуальную машину. Необходимо в настройках сети выбрать тип подключения "Сетевой мост". В этом случае виртуальная машина подключается напрямую к основной сети как полноценное устройство
<p align="center"><img src="https://github.com/user-attachments/assets/d9191e1a-b4d6-451d-8d09-fff07424e9fd" width=700></p>

Получим ip адрес с помощью команды /ip address print
<p align="center"><img src="https://github.com/user-attachments/assets/84435786-be88-4bb0-a791-dd007ca7570d" width=700></p>

Установим WinBox и подключимся по полученному адресу
<p align="center"><img src="https://github.com/user-attachments/assets/c5d8a594-5cc3-479c-949e-9ab1ff664830" width=700></p>

На виртуальной машине установим wireguard
<p align="center"><img src="https://github.com/user-attachments/assets/7d7b3fbd-dfec-4fe6-b7b6-97a3a7aaf4c4" width=700></p>

Создаем приватный и публичный ключи
<p align="center"><img src="https://github.com/user-attachments/assets/3d8594d1-04f5-4930-9125-1cafb482e078" width=700></p>

Далее создаем файл конфигурации интерфейса wireguard, указываем в публичном ключе публиный ключ роутера, полученный в WinBox
<p align="center"><img src="https://github.com/user-attachments/assets/576a1bb6-160e-45fa-b991-0ebb77a6629a" width=700></p>

Создаем peer в WinBox, указывая публичный ключ сгенерированный на ВМ и endpoint - ip ВМ, allowed address - адрес, прописанный в конфигурационном файле
<p align="center"><img src="https://github.com/user-attachments/assets/3aec1f7f-7ef2-41ec-ab1e-48f82e9bf7ff" width=700></p>

Запускаем wireguard на ВМ
<p align="center"><img src="https://github.com/user-attachments/assets/08457dae-af4d-47da-8f36-86fe3203321b" width=700></p>

Далее тестируем соединение
Сервер - Клиент
<p align="center"><img src="https://github.com/user-attachments/assets/fb2eff0f-459b-498e-8a94-51e15150778b" width=700></p>

Клиент - Сервер
<p align="center"><img src="https://github.com/user-attachments/assets/549327bd-3352-47c1-a252-2b10e8df8322" width=700></p>

Проверка интернет соединения с сервера
<p align="center"><img src="https://github.com/user-attachments/assets/b29d37de-3f25-4891-871b-47c201b8dc46" width=700></p>

Проверка интернет соединения с клиента
<p align="center"><img src="https://github.com/user-attachments/assets/dc97f148-1243-4d69-8dc9-73cb5857eaa6" width=700></p>

### Вывод
В данной лабораторной работе был поднят сервер с помощью Yandex Cloud и CHR с помощью VirtualBox. Далее были настроены wireguard интерфейсы с помощью WinBox и файла конфигурации и произведена проверка соединения между клиентом и сервером.










