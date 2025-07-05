## Домашнее задание к занятию 13.1 «Уязвимости и атаки на информационные системы» - Гурылев Александр

# Задание 1
Устанавлваем VMware Workstation Player (Ubuntu)

sudo apt update

sudo apt install -y build-essential linux-headers-generic

wget --user-agent="Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0" https://www.vmware.com/go/getplayer-linux

sudo chmod +x getplayer-linux

sudo ./getplayer-linux --required --eulas-agreed

Сканируем виртуальную машину

nmap -sSV 172.16.52.128 # инфомация по открытым TCP портам (TCP SYN)

nmap -sTV 172.16.52.128 # инфомация по открытым TCP портам (TCP Connect)

nmap -sAV 172.16.52.128 # инфомация по открытым TCP портам (TCP FIN)

nmap -sUV 172.16.52.128 # инфомация по открытым UDP портам

nmap -sV 172.16.52.128 # инфомация по всем открытым TCP портам

nmap -A 172.16.52.128 # агресивный режим - инфомация по всем открытым TCP портам

Список нескольких уязвимостей

vsftpd 2.3.4 - Backdoor Command Execution

TelnetD encrypt_keyid - Function Pointer Overwrite

ProFTPd IAC 1.3.x - Remote Command Execution

MySQL 5.0.x - IF Query Handling Remote Denial of Service

MySQL 5.0.x - Single Row SubSelect Remote Denial of Service

# Задание 2

Проведите сканирование Metasploitable в режимах SYN, FIN, Xmas, UDP.

Запишите сеансы сканирования в Wireshark.

Ответьте на следующие вопросы:

Чем отличаются эти режимы сканирования с точки зрения сетевого трафика?
Как отвечает сервер?
Приведите ответ в свободной форме.

Сеансы сканирования
nmap-SYN.pcapng
![alt text](https://github.com/A1ex93/infobez_1/blob/main/2.png)
nmap-FIN.pcapng
![alt text](https://github.com/A1ex93/infobez_1/blob/main/3.png)
nmap-Xmas.pcapng
![alt text](https://github.com/A1ex93/infobez_1/blob/main/1.png)
nmap-UDP.pcapng
![alt text](https://github.com/A1ex93/infobez_1/blob/main/4.png)
SYN сканирование
SIN - сканирование с установкой полуоткрытого соединения, так как соединение не устанавливается до конца. На определенный порт посылается сообщение SYN, затем идет ожидание ответа, на основании которого определяется статус порта. Ответ SYN-ACK говорят о том, что порт прослушивается (состояние «открыт»), а ответ RST говорит о том, что не прослушивается. Если после нескольких запросов не приходит никакого ответа или в ответ приходит сообщение ICMP с ошибкой достижимости (Destination Unreachable), то статус порта определяется в состоянии «фильтруется».
![alt text](https://github.com/A1ex93/infobez_1/blob/main/wireshark_SS.png)
FIN сканирование
FIN - сканирование основано на отправке запросов, содержащих только один установленный флаг контроля соединения - FIN. В отличие от запросов SYN, многие брандмауэры не блокируют такой PDU (Protocol Data Unit). Закрытый порт, как правило, отвечает на них ответом RST (разрыв соединения). Открытый порт - не отвечает.
![alt text](https://github.com/A1ex93/infobez_1/blob/main/wireshark_SF.png)
Xmas сканирование
Xmas — сканирование основано на отправке запросов, содержащих разные флаги из всех доступных для контроля соединения. Сканирование Xmas возвращает три возможных состояния порта:
![alt text](https://github.com/A1ex93/infobez_1/blob/main/wireshark_SX.png)
Open-filtered - не возможно определить, открыт порт или отфильтрован. Даже если порт открыт, сканирование Xmas сообщит о нем как open-filtered. Это происходит, когда не получен ответ (даже после повторной передачи).
Closed - если в ответ приходит сообщение RST.
Filtered - брандмауэр фильтрует сканируемые порты; это происходит, когда ответом является ошибка ICMP unreachable (тип 3, код 1, 2, 3, 9, 10 или 13).
UDP сканирование
Сканирование медленное из-за специфики UDP и возможности потери пакетов запросы повторяются несколько раз (обычно три и более). С закрытого порта придет ICMP-сообщение об ошибке (Destination Unreachable). Если ответ не получен, статус порта определяется в состоянии «открыт» или «фильтруется», поскольку непонятно, что стало причиной - блокировка трафика средством защиты или потеря пакетов. В нашем случае, например, для сканирования порта назначения 1013 было отправлено 10 пакетов с разных портов источников. С точки зрения нагрузки на сеть данный тип сканирования самый ресурсоёмкий.
![alt text](https://github.com/A1ex93/infobez_1/blob/main/wireshark_SU.png)
