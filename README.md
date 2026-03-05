# Домашнее задание к занятию «Уязвимости и атаки на информационные системы» - `Корниенко Кирилл`


### Задание 1. 

### Кейс
Скачайте и установите виртуальную машину Metasploitable: [https://sourceforge.net/projects/metasploitable/](https://sourceforge.net/projects/metasploitable/)

Это типовая ОС для экспериментов в области информационной безопасности, с которой следует начать при анализе уязвимостей.

Просканируйте эту виртуальную машину, используя nmap.

Сами уязвимости можно поискать на сайте [https://www.exploit-db.com/](https://www.exploit-db.com/)

Для этого нужно в поиске ввести название сетевой службы, обнаруженной на атакуемой машине, и выбрать подходящие по версии уязвимости.

Ответьте на следующие вопросы:

- Какие сетевые службы в ней разрешены?

- Какие уязвимости были вами обнаружены? (список со ссылками: достаточно трёх уязвимостей)

*Приведите ответ в свободной форме.*

### Ответ:

- Какие сетевые службы в ней разрешены?

![nmap](https://github.com/kirill-kornienko/Backup/blob/main/nmap.png)

- Какие уязвимости были вами обнаружены? (список со ссылками: достаточно трёх уязвимостей)

1. vsftpd 2.3.4 - [Backdoor Command Execution (Metasploit)](https://www.exploit-db.com/exploits/17491)

2. Samba 3.0.20  - ['Username' map script' Command Execution (Metasploit)](https://www.exploit-db.com/exploits/16320)

3. UnrealIRCd 3.2.8.1 - [Backdoor Command Execution (Metasploit)](https://www.exploit-db.com/exploits/16922)




