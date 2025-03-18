University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Network programming](https://github.com/itmo-ict-faculty/network-programming)  
Year: 2024/2025  
Group: K34212  
Author: Aukhadyev Ruslan Radikovich   
Lab: Lab4    
Date of create: 18.03.2025  
Date of finished: 18.03.2025  

## Отчёт о Лабораторной работе №4 <br>"Базовая 'коммутация' и туннелирование используя язык программирования P4"

### Описание работы

В данной лабораторной работе вы познакомитесь на практике с языком программирования P4, разработанным компанией Barefoot (ныне Intel) для организации процесса обработки сетевого трафика на скорости чипа. Barefoot разработал несколько FPGA-чипов для обработки трафика, которые были встроены в некоторые модели коммутаторов Arista и Brocade.

### Цель работы

Изучить синтаксис языка программирования P4 и выполнить два обучающих задания от Open Networking Foundation для ознакомления с практическим применением P4.

### Ход работы

Скачаем и установим p4-tutorial ВМ в VirtualBox



В первом задании необходимо написать программу на P4 для базовой пересылки IPv4-пакетов. С пересылкой IPv4 коммутатор должен выполнить следующие действия для каждого пакета: (i) обновить MAC-адреса источника и назначения, (ii) уменьшить время жизни (TTL) в заголовке IP и (iii) переслать пакет через соответствующий порт.
Схема, которая должна получиться



Переходим в директорию первого задания и запустим make run, проверим пинг


В директории уже есть заготовка для решения, прописаны заголовки 



Далее необходимо добавить код парсера, который будет извлекать заголовки
```
parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start {
        transition parse_ethernet;
    }

    state parse_ethernet {
        packet.extract(hdr.ethernet);
        transition select(hdr.ethernet.etherType) {
            TYPE_IPV4: parse_ipv4;
            default: accept;
        }
    }

    state parse_ipv4 {
        packet.extract(hdr.ipv4);
        transition accept;
    }
}
```

Далее добавим входную обработку. Если маршрут найден - вызываем ipv4_forward, если не найдем - отбрасываем пакет drop().
```
control MyIngress(inout headers hdr,
                  inout metadata meta,
                  inout standard_metadata_t standard_metadata) {

    action drop() {
        mark_to_drop(standard_metadata);
    }

    action ipv4_forward(macAddr_t dstAddr, egressSpec_t port) {
        standard_metadata.egress_spec = port;
        hdr.ethernet.srcAddr = hdr.ethernet.dstAddr;
        hdr.ethernet.dstAddr = dstAddr;
        hdr.ipv4.ttl = hdr.ipv4.ttl - 1;
    }

    table ipv4_lpm {
        key = {
            hdr.ipv4.dstAddr: lpm;
        }
        actions = {
            ipv4_forward;
            drop;
            NoAction;
        }
        size = 1024;
        default_action = drop();
    }

    apply {
        if (hdr.ipv4.isValid()) {
            ipv4_lpm.apply();
        }
    }
}
```

В депарсере добавляем обратно заголовки ethernet и ipv4
```
control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        packet.emit(hdr.ipv4);
    }
}
```

Запускаем решение, проверяем доступность узлов



Следующим заданием было добавить поддержку туннелирования к маршрутизатору IPv4, используя новый заголовок myTunnel_t для маршрутизации по полю dst_id.
Возмьем шаблон basic_tunnel.p4. Добавим поддержку myTunnel_t в парсер
```
parser MyParser(packet_in packet,
                out headers hdr,
                inout metadata meta,
                inout standard_metadata_t standard_metadata) {

    state start {
        transition parse_ethernet;
    }

    state parse_ethernet {
        packet.extract(hdr.ethernet);
        transition select(hdr.ethernet.etherType) {
            TYPE_MYTUNNEL: parse_myTunnel;
            TYPE_IPV4: parse_ipv4;
            default: accept;
        }
    }

    state parse_myTunnel {
        packet.extract(hdr.myTunnel);
        transition select(hdr.myTunnel.proto_id) {
            TYPE_IPV4: parse_ipv4;
            default: accept;
        }
    }

    state parse_ipv4 {
        packet.extract(hdr.ipv4);
        transition accept;
    }
}
```

Во входную обработку добавим создание таблицы
```
control MyIngress(inout headers hdr,
                  inout metadata meta,
                  inout standard_metadata_t standard_metadata) {
    action drop() {
        mark_to_drop(standard_metadata);
    }

    action ipv4_forward(macAddr_t dstAddr, egressSpec_t port) {
        standard_metadata.egress_spec = port;
        hdr.ethernet.srcAddr = hdr.ethernet.dstAddr;
        hdr.ethernet.dstAddr = dstAddr;
        hdr.ipv4.ttl = hdr.ipv4.ttl - 1;
    }

    table ipv4_lpm {
        key = {
            hdr.ipv4.dstAddr: lpm;
        }
        actions = {
            ipv4_forward;
            drop;
            NoAction;
        }
        size = 1024;
        default_action = drop();
    }

    action myTunnel_forward(egressSpec_t port) {
    standard_metadata.egress_spec = port;
    }
    
    table myTunnel_exact {
        key = {
            hdr.myTunnel.dst_id: exact;
        }
        actions = {
            myTunnel_forward;
            drop;
            NoAction;
        }
        size = 1024;
        default_action = drop();
    }

    apply {
        if (hdr.myTunnel.isValid()) {
            myTunnel_exact.apply();
        } else if (hdr.ipv4.isValid()) {
            ipv4_lpm.apply();
        }
    }
}

```

В депарсер добавим заголовок myTunnel_t
```
control MyDeparser(packet_out packet, in headers hdr) {
    apply {
        packet.emit(hdr.ethernet);
        packet.emit(hdr.myTunnel);
        packet.emit(hdr.ipv4);
    }
}
```

Соберем и запустим



Для проверки доступности открываем терминалы h1 (10.0.1.1) и h2 (10.0.2.2). Отправим с h2 на h1 сообщение



Проверим с h1 на h2 туннелирование командой ./send.py 10.0.2.2 "ping" --dst_id 2



И проверим указав IP h3 ./send.py 10.0.3.3 "ping" --dst_id 2



### Вывод
В данной лабораторной работе были изучены основы языка P4, освоена настройка виртуальной среды для разработки, а также реализована базовая пересылка IPv4-пакетов и туннелирование с использованием пользовательского заголовка.
