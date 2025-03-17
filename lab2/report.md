University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34212  
Author: Aukhadyev Ruslan Radikovich 
Lab: Lab2  
Date of create: 17.03.2024
Date of finished: 17.03.2024  

## Отчёт о Лабораторной работе №2 <br>"Развертывание дополнительного CHR, первый сценарий Ansible"

### Описание работы

В данной лабораторной работе вы на практике ознакомитесь с системой управления конфигурацией Ansible, использующаяся для автоматизации настройки и развертывания программного обеспечения.


### Цель работы

С помощью Ansible настроить несколько сетевых устройств и собрать информацию о них. Правильно собрать файл Inventory.


### Ход работы

Создаем вторую машину CHR в VirtualBox

![image](https://github.com/user-attachments/assets/4a178114-d55b-4ea7-8b1b-9efb42a2e72a)

Далее, как и в первой работе, получаем ip адрес chr2, подключаемся с WinBox и настраиваем интерфейс wireguard

![image](https://github.com/user-attachments/assets/d7053f14-d635-4c68-bed2-ea58df7e8d4d)

Добавляем Peer в файл конфигурации wireguard на ВМ

![image](https://github.com/user-attachments/assets/b4ff1f1d-0d3e-43e5-a019-bbf8ff74418a)

Перезапускаем wireguard на ВМ и проверяем подключение

![image](https://github.com/user-attachments/assets/04fce249-a5f0-41b7-be7f-47fe849b817f)

Далее необходимо настроить NTP и OSPF с помощью Ansible. Для начала на роутерах вклюим ssh

![image](https://github.com/user-attachments/assets/a8e040a9-9aaf-43a3-a125-fc7d1f6424e5)

Попробуем подключиться с ВМ по ssh

![image](https://github.com/user-attachments/assets/1de5f0e6-22c8-4183-966c-97da994a70c7)

Затем необходимо создать инвентарный файл inventory.yml с описанием используемых роутеров

![image](https://github.com/user-attachments/assets/f504da77-478a-49f5-beaf-45b8a0d3dd88)

Попробуем запустить ansible с инвентарным файлом, увидим ошибку

![image](https://github.com/user-attachments/assets/b0871bc1-e4b0-4625-b958-cda088b193dc)

Необходимо было в ansible_connection указать network_cli, запустим еще раз

![image](https://github.com/user-attachments/assets/3e64f837-e086-430b-8598-8142ac6fd6dc)

Напишем код для playbook с конфигурацией NTP и OSPF

```
- name: "playbook"
  hosts: chrs

  tasks:
    - name: auth
      community.routeros.command:
        commands: "user add name=ansible_user password=ansible_user group=full"

    - name: ntp
      community.routeros.command:
        commands: "system ntp client set enabled=yes servers=8.8.8.8"

    - name: ospf
      community.routeros.command:
        commands:
          - /interface bridge add name=Lo
          - /ip address add address="{{ router_id }}"/32 interface=Lo
          - /routing ospf instance add name=v2inst version=2 router-id="{{ router_id }}"
          - /routing ospf area add name=backbone_v2 area-id=0.0.0.0 instance=v2inst
          - /routing ospf interface-template add network=0.0.0.0/0 area=backbone_v2

    - name: get_ospf
      community.routeros.command:
        commands: "/routing ospf neighbor print"
      register: ospf_info

    - name: get_conf
      community.routeros.facts:
        gather_subset:
          - config
      register: config_info

    - name: ospf_info
      debug:
        msg: "{{ ospf_info }}"

    - name: config_info
      debug:
        msg: "{{ config_info }}"
```

Запустим командой ansible-playbook

![image](https://github.com/user-attachments/assets/92d57d4b-39c3-4498-a3ef-211145559a6a)

Все прошло успешно, проверим ospf

![image](https://github.com/user-attachments/assets/a59ca478-871a-4499-8c77-e9fb64b23d18)
![image](https://github.com/user-attachments/assets/d8c24cca-ccdf-4ec4-95c5-46976c4f7e9f)


### Вывод
В данной лабораторной работе была произведена установка ansible на ВМ, создание второго CHR и конфигурация инвентарного файла для загрузки параметров на оба CHR с помощью плейбука.



