ISP-HQ — 172.16.4.0 / 28 (не более 8) [VLAN999]

ISP-BR — 172.16.5.0 / 28 (не более 8) [VLAN999]

INTERNET — 10.10.2.0 / 29 (не более 8) [VLAN999]

VM Network — DHCP

HQ-NET — 192.168.1.0 / 29 (не более 64)

BR-NET — 192.168.3.0 / 29 (не более 32)
________________________________________
1.	Сделать везде Restore Snapshot 
2.	Добавить везде Network Adapter
3.	Сверить ens с MAC-адресами и записать в WORD
4.	Задать hostname (ISP.au-team.irpo) [mcedit /etc/hostname]
5.	Прописать [domainname au-team.irpo]
6.	Создать папки для всех ens [mkdir]
7.	Скопировать ens192/options на все [cp]
8.	Отредактировать ens192/options (DHCP)
9.	Выдать IP на ens (кроме ens192) [ipv4address]
10.	Прописать шлюз для конечных устройств [ipv4route]
________________________________________
1.	Обновить репозитории на всех роутерах (3) [apt-get update]
2.	Добавить FRR на роутеры [apt-get install frr]
3.	Добавить FRR в автозагрузку [systemctl enable  --now frr.service] + [systemctl status frr.service]
4.	Включаем протокол eigrp [mcedit /etc/frr/daemons], затем [systemctl restart frr.service]
5.	vtysh
6.	conf t
7.	router eigrp 1
8.	network ISP-HQ
9.	network ISP-BR
10.	network INTERNET
11.	do wr
12.	exit exit exit
13.	ip r
14.	Заменить 0 на 1 на роутерах (3) [mcedit /etc/net/sysctl.conf] (net.ipv4.ip_forward)
15.	Затем пишем - sysctl -p
16.	sysctl net.ipv4.ip_forward=1
17.	vtysh
18.	show ip eigrp topology
________________________________________
1.	Добавить пользователя на HQ-SRV / BR-SRV [useradd sshuser -u 1010] *
2.	Установить пароль на пользователя [passwd sshuser] + [control sudoers]
3.	Добавить пользователя в список sudo [usermod -aG wheel sshuser] + [id sshuser]
4.	Разрешаем всем в wheel пользоваться sudo [mcedit /etc/sudoers] убирая две предпоследние #

5.	Добавить пользователя на HQ-RTR / BR-RTR [useradd net_admin] *
6.	Установить пароль на пользователя [passwd net_admin] + [control sudoers]
7.	Добавить пользователя в список sudo [usermod -aG wheel net_admin] + [id sshuser]
8.	Разрешаем всем в wheel пользоваться sudo [mcedit /etc/sudoers] убирая две предпоследние #
________________________________________
1.	Обновить репозитории на HQ-SRV / BR-SRV [apt-get update]
2.	Установить openssh-server [apt-get install openssh-server -y], затем [systemctl daemon-reload]
3.	Добавляем openssh-server в автозагрузку [systemctl enable  --now sshd]
4.	Убираем # и меняем: Port; MaxAuthTries. Прописываем путь к Banner /etc/banner; Добавляем AllowUsers sshuser [mcedit /etc/openssh/sshd_config]
5.	Добавляем текст в файл [mcedit /etc/banner]
6.	Перезапускаем [systemctl restart sshd.service]

![image](https://github.com/user-attachments/assets/4683ab8d-76d2-4c0a-8fc1-6e5980cd3c79)
![image](https://github.com/user-attachments/assets/0f159181-aa18-468c-ad20-b8e15b87a93a)
