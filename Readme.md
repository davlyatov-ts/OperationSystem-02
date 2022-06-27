-	Домашнее задание к занятию "3.4. Операционные системы, лекция 2"
____________________________________________________________________________
1. На лекции мы познакомились с **node_exporter**. В демонстрации его исполняемый файл запускался в background.<br> 
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением.<br> 
Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:<br>

-	поместите его в автозагрузку,
-	предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на 
-	systemctl cat cron),
-	удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
-	
-	Ответ
-	sudo systemctl enable --now node_exporter.service;
-	sudo nano /etc/systemd/system/node_exporter.service
-	  GNU nano 6.2    /etc/systemd/system/node_exporter.service                                                       
-	[Unit]
-	Description=Node Exporter
-	Wants=network-online.target
-	After=network-online.target
-
-	[Service]
-	User=node_exporter
-	Group=node_exporter
-	Type=simple
-	ExecStart=/usr/local/bin/node_exporter
-
-	[Install]
-	WantedBy=multi-user.target
-
-	systemctl status node_exporter
-
-	● node_exporter.service - Node Exporter
-	Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
-	Active: active (running) since Tue 2022-06-21 20:29:08 MSK; 20min ago
-	Main PID: 17503 (node_exporter)
-	Tasks: 7 (limit: 4583)
-	Memory: 4.8M
-       CPU: 230ms
-       CGroup: /system.slice/node_exporter.service
-	        └─17503 /usr/local/bin/node_exporter
-
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=thermal_zone<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=time<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=timex<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=udp_queues<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=uname<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=vmstat<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=xfs<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.628Z caller=node_exporter.go:115 level=info collector=zfs.
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.629Z caller=node_exporter.go:199 level=info msg="Listening on" address=:9100<br>
июн 21 20:29:08 pi node_exporter[17503]: ts=2022-06-21T17:29:08.629Z caller=tls_config.go:195 level=info msg="TLS is disabled." http2=false<br>
-	
-![http://localhost:9100/metrics](https://github.com/davlyatov-ts/OperationSystem-02/blob/master/9100.png)
_________________________________________________________________________________________________________________________
-	2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. Приведите несколько опций, которые вы бы выбрали для 
-	базового мониторинга хоста по CPU, памяти, диску и сети.
-	
-	ОТвет
-	CPU:
-	    node_cpu_seconds_total{cpu="0",mode="idle"} 2238.49
-	    node_cpu_seconds_total{cpu="0",mode="system"} 16.72
-	    node_cpu_seconds_total{cpu="0",mode="user"} 6.86
-	    process_cpu_seconds_total
-    
-	Memory:
-	    node_memory_MemAvailable_bytes 
-	    node_memory_MemFree_bytes
-    
-	Disk(если несколько дисков то для каждого):
-	    node_disk_io_time_seconds_total{device="sda"} 
-	    node_disk_read_bytes_total{device="sda"} 
-	    node_disk_read_time_seconds_total{device="sda"} 
-	    node_disk_write_time_seconds_total{device="sda"}
    
-	Network(так же для каждого активного адаптера):
-	    node_network_receive_errs_total{device="eth0"} 
-	    node_network_receive_bytes_total{device="eth0"} 
-	    node_network_transmit_bytes_total{device="eth0"}
-	    node_network_transmit_errs_total{device="eth0"}
____________________________________________________________________________________________________________________________
-	3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). 
-	После успешной установки:
-
-	в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,
-	добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:
-	config.vm.network "forwarded_port", guest: 19999, host: 19999
-	После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на localhost:19999. 
-	Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
-
-	Ответ
-	установлена netdata, но проброшен порт 55777, так как 19999 - занять на хостовой машине под локальный netdata 
-	информация с хостовой машины:
-	22:23:36 ➜  ~/vagrant$ sudo lsof -i :19999
-	COMMAND   PID    USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
-	netdata 50358 netdata    4u  IPv4 1003958      0t0  TCP localhost:19999 (LISTEN)
-	22:23:36 ➜ ~/vagrant$ sudo lsof -i :5777
-	COMMAND     PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
-	chrome     4089 pi   80u  IPv4 1112886      0t0  TCP localhost:38598->localhost:55777 (ESTABLISHED)
-	VBoxHeadl 52075 pi   21u  IPv4 1053297      0t0  TCP *:55777 (LISTEN)
-	VBoxHeadl 52075 pi   30u  IPv4 1113792      0t0  TCP localhost:55777->localhost:38598 (ESTABLISHED)
-
-	информация с vm машины:
-	vagrant@vagrant:~$ sudo lsof -i :19999
-	COMMAND  PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
-	netdata 1895 netdata    4u  IPv4  30971      0t0  TCP *:19999 (LISTEN)
-	netdata 1895 netdata   55u  IPv4  31861      0t0  TCP vagrant:19999->_gateway:38598 (ESTABLISHED)
-	[netdata](https://github.com/davlyatov-ts/OperationSystem-02/blob/master/Screenshot_1.png)
_______________________________________________________________________________________________________
-	4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
-	
-	Ответ
-	dmesg |grep virtualiz
-	[    0.295331] Booting paravirtualized kernel on VMware hypervisor
-	[    4.524931] systemd[1]: Detected virtualization vmware.
-	с хостовой машиной то это становится очевидным (ps:хорошо когда такая есть под рукой для обучения
-	dmesg |grep virtualiz
-	[    0.295331] Booting paravirtualized kernel on bare hardware
______________________________________________________________________________________________________________
-	5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. Какой другой существующий 
-	лимит не позволит достичь такого числа (ulimit --help)?
-	
-	Ответ
-	vagrant@vagrant:~$ /sbin/sysctl -n fs.nr_open
-	1048576

-	Это максимальное число открытых дескрипторов для ядра (системы), для пользователя задать больше этого числа нельзя (если не менять). 
-	Число задается кратное 1024, в данном случае =1024*1024. 
-
-	Но макс.предел ОС можно посмотреть так :
-	vagrant@vagrant:~$ cat /proc/sys/fs/file-max
-	9223372036854775807
-
-	vagrant@vagrant:~$ ulimit -Sn
-	1024
-
-	мягкий лимит (так же ulimit -n)на пользователя (может быть увеличен процессов в процессе работы)
-
-	vagrant@vagrant:~$ ulimit -Hn
-	1048576
-
-	жесткий лимит на пользователя (не может быть увеличен, только уменьшен)
-
-	Оба ulimit -n НЕ могут превысить системный fs.nr_open
__________________________________________________________________________________________________________________
-	Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) в отдельном неймспейсе процессов; 
-	покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в данном задании под root (sudo -i). 
-	Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
-	
-	ответ
-	root@vagrant:~# ps -e |grep sleep
-	   2022 pts/2    00:00:00 sleep
-	root@vagrant:~# nsenter --target 2022 --pid --mount
-	root@vagrant:/# ps
-	    PID TTY          TIME CMD
-	      2 pts/0    00:00:00 bash
-	     11 pts/0    00:00:00 ps
__________________________________________________________________________________________________________________________________________-
-	Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине Vagrant с Ubuntu 20.04 
-	(это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", после чего (минуты) – ОС должна стабилизироваться. 
-	Вызов dmesg расскажет, какой механизм помог автоматической стабилизации. Как настроен этот механизм по-умолчанию, 
-	и как изменить число процессов, которое можно создать в сессии?
-	
-	Ответ
-	Из предыдущих лекций ясно что это функция внутри "{}", судя по всему с именем ":" , которая после опредения в строке запускает саму себя.
-	внутринности через поиск нагуглил, пораждает два фоновых процесса самой себя,
-	получается этакое бинарное дерево плодящее процессы 

-	А функционал судя по всему этот:
-	[ 3099.973235] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-4.scope
-	[ 3103.171819] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-11.scope
-
-	Судя по всему, система на основании этих файлов в пользовательской зоне ресурсов имеет определенное ограничение на создаваемые ресурси 
-	и соответсвенно при превышении начинает блокировать создание числа 
-
-	Если установить ulimit -u 50 - число процессов будет ограниченно 50 для пользоователя. 
__________________________________________________________________________________________________________________________________________
