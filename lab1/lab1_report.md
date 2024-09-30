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

Далее создаем файл конфигурации интерфейса wireguard
<p align="center"><img src="https://github.com/user-attachments/assets/3ac7242b-b78e-44da-9c2e-7f2a4e899c2b" width=700></p>

Запускаем wireguard на сервере
<p align="center"><img src="https://github.com/user-attachments/assets/14f692a0-2ebd-4c1c-8abe-289379f0bece" width=700></p>







