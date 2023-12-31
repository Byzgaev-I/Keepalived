# Домашнее задание к занятию «Disaster recovery и Keepalived» - Бызгаев Александр

### Задание 1

- Дана для Cisco Packet Tracer, рассматриваемая в лекции.
- На данной схеме уже настроено отслеживание интерфейсов маршрутизаторов Gi0/1 (для нулевой группы)
- Необходимо аналогично настроить отслеживание состояния интерфейсов Gi0/0 (для первой группы).
- Для проверки корректности настройки, разорвите один из кабелей между одним из маршрутизаторов и Switch0 и запустите ping между PC0 и Server0.
- На проверку отправьте получившуюся схему в формате pkt и скриншот, где виден процесс настройки маршрутизатора.

 ---

### Выполнения задания 1

- Сконфигурировал маршрутизатор: Router1
- Проверил текущую конфигурацию
- Настроил интерфейс
- Выставил приоритет 

![Image alt](https://github.com/Byzgaev-I/Keepalived/blob/main/Keepalive%201.png)

[Схема решения](https://github.com/Byzgaev-I/Keepalived/blob/main/1-hsrp_advanced_Netology.pkt)

### Задание 2

- Запустите две виртуальные машины Linux, установите и настройте сервис Keepalived как в лекции.
- Настройте любой веб-сервер на двух виртуальных машинах
- Напишите Bash-скрипт, который будет проверять доступность порта данного веб-сервера и существование файла index.html в root-директории данного веб-сервера.
- Настройте Keepalived так, чтобы он запускал данный скрипт каждые 3 секунды и переносил виртуальный IP на другой сервер, если bash-скрипт завершался с кодом, отличным от нуля. Используйте для этого секцию vrrp_script
- На проверку отправьте получившейся bash-скрипт и конфигурационный файл keepalived, а также скриншот с демонстрацией переезда плавающего ip на другой сервер в случае недоступности порта или файла index.html

### Выполнения задания 2

Создал две машины.
Настроил веб-сервер.

![Image alt](https://github.com/Byzgaev-I/Keepalived/blob/main/M%20-%20B.png)

![Image alt](https://github.com/Byzgaev-I/Keepalived/blob/main/NGINX.png)



bash-скрипт
### bash.sh
```bash
my_index=`test -f /var/www/html/index.html && echo $?`
my_port=`bash -c "</dev/tcp/localhost/80" && echo $?`

if [ $my_index -eq 0 ] && [ $my_port -eq 0 ]; then
        exit 0
else
        exit 1
fi
```
![Image alt](https://github.com/Byzgaev-I/Keepalived/blob/main/BASH.png)

Конфигурация keepalived

### keepalived.conf
```
global_defs {
    script_user root
    enable_script_security
}

vrrp_script check_srv {
    script "/etc/keepalived/keep_script.sh"
    interval 3
}

vrrp_instance VI_1 {
        state MASTER
        interface enp0s3
        virtual_router_id 15
        priority 100
        advert_int 1

        virtual_ipaddress {
                192.168.0.115/24
        }
        track_script {
           check_nginx
        }
}
```
![Image alt](https://github.com/Byzgaev-I/Keepalived/blob/main/CONF.png)
