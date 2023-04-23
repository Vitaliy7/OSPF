# OSPF
## Домашнее задание по теме _Статическая и динамическая маршрутизация, OSPF_  
> 1. Поднять три виртуалки  
> 2. Объединить их разными vlan  
> * поднять OSPF между машинами на базе Quagga;  
> * изобразить ассиметричный роутинг;  
> * сделать один из линков "дорогим", но что бы при этом роутинг был симметричным.  

### Решение ДЗ.  
1. Выполнение происходит автоматически, при помощи _Vagrant+Ansible_. Задание сделано согласно методичке, с небольшими изменениями.  
Для выполнения ДЗ по очереди вначале запускаем _vagrant up --no-provision_,  а затем выполняем провижн для первого задания:  

_ansible-playbook ansible/provision.yml -i ansible/hosts --skip-tags asymmetric, symmetric -e "host_key_checking=false"_  

Плейбук аналогичен тому, что представлен в задании, за исключением того, что файл _/etc/frr/daemons_ не скачивается с хост-платформы, а редактируется 
непосредственно на гостевых машинах при помощи модуля _ansible_ _lineinfile_.  
Заходим на _router1_ и пингуем, а затем и трассируем адрес _192.168.30.1_. Отключаем интерфейс _enp0s9_, немного ждем и снова запускаем
трассировку до ip-адреса _192.168.30.1_  Видим, что после отключения сети на _enp0s9_ роутинг изменился, но сеть _192.168.30.0/24_ осталась доступна:  

![](https://github.com/Vitaliy7/OSPF/blob/main/screenshots/1.png?raw=true)  

2. Ассимитричный роутинг, после _vagrant up --no-provision_ поднимается командой  

_ansible-playbook ansible/provision.yml -i ansible/hosts -t asymmetric -e "host_key_checking=false"_  

Стоимость интерфейса _enp0s8_ на _router1_ меняется при помощи теплейта _asymmetric_frr.conf.j2_.  
Заходим на _router1_ и проверяем настройки: 

![](https://github.com/Vitaliy7/OSPF/blob/main/screenshots/2.png?raw=true)

Проверяем выполнение:  
На _router1_ запускаем пинг:  

![](https://github.com/Vitaliy7/OSPF/blob/main/screenshots/3.png?raw=true)

На _router2_ запускаем _tcpdump_ и смотрим трафик на интерфейсе _enp0s9_  

![](https://github.com/Vitaliy7/OSPF/blob/main/screenshots/4.png?raw=true)  

3. Настройка симметичного роутинга  
Можно просто запустить _vagrant up_, на _router1_ выполнить _ping -I 192.168.10.1 192.168.20.1_, на _router2_ запустить _tcpdump -i enp0s9_  
и смотрим результат:  

![](https://github.com/Vitaliy7/OSPF/blob/main/screenshots/5.png?raw=true)  

Убеждаемся в том, что трафик между роутерами ходит симметрично.


