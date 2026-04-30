# 🖥️ Отчет по машине: SimpleCTF

**Дата:** 2026-04-30
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

---

## 📡 1. Разведка 

Просмотрел сайт и это стандартная страница запука сервера убунту, коминтариев или какой-то полезной инфы нет.
<img width="902" height="683" alt="Снимок экрана 2026-04-30 в 06 47 04" src="https://github.com/user-attachments/assets/96d13173-0919-4b5d-aaaf-ebdcaf06f2f3" />

Дальше делаю перебор путей
цель: найти креды или открытые пути с файлами 
### Dirsearch
```
dirsearch -u http://10.129.140.247
```
Нашел 1 путь и 1 файл 
```
curl http://10.129.140.247/robots.txt
```
Смотрю что нашел Dirsearch
```      
#
# "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $"
#
#   This file tells search engines not to index your CUPS server.
#
#   Copyright 1993-2003 by Easy Software Products.
#
#   These coded instructions, statements, and computer programs are the
#   property of Easy Software Products and are protected by Federal
#   copyright law.  Distribution and use rights are outlined in the file
#   "LICENSE.txt" which should have been included with this file.  If this
#   file is missing or damaged please contact Easy Software Products
#   at:
#
#       Attn: CUPS Licensing Information
#       Easy Software Products
#       44141 Airport View Drive, Suite 204
#       Hollywood, Maryland 20636-3111 USA
#
#       Voice: (301) 373-9600
#       EMail: cups-info@cups.org
#         WWW: http://www.cups.org
#

User-agent: *
Disallow: /
Disallow: /openemr-5_0_1_3 
#
# End of "$Id: robots.txt 3494 2003-03-19 15:37:44Z mike $".
#
```
Есть пользователь Mike и путь ведущий на страницу с сайтом блогом 

```
curl http://10.129.140.247/simple
```
```   
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://10.129.140.247/simple/">here</a>.</p>
<hr>
<address>Apache/2.4.18 (Ubuntu) Server at 10.129.140.247 Port 80</address>
</body></html>
```
<img width="1411" height="648" alt="Снимок экрана 2026-04-30 в 06 55 40" src="https://github.com/user-attachments/assets/77be6af8-c9b4-426f-b815-27c890cd0416" />

Изучив блог заметил часто повторяющееся имя mitch 
<img width="837" height="269" alt="Снимок экрана 2026-04-30 в 06 54 24" src="https://github.com/user-attachments/assets/3e86ece3-68cc-4784-98b7-59d66e5a9c92" />


Так как вывело на полноценный сайт запустил сканирование еще раз на путь simple 
цель: найти креды, комментарии или страницу входа
```
dirsearch -u http://10.129.140.247/simple
```
<img width="801" height="373" alt="Снимок экрана 2026-04-30 в 07 05 38" src="https://github.com/user-attachments/assets/60d51337-37a5-43d0-a212-0968785597f7" />
есть админская панель со страницей входа и множество путей, однако доступ к ним ограничен обычному пользователю, здесь пока сбор данных закончил, переключусь на сканирование открытых портов 

### Nmap
запустил сканирование портов, чтобы увидеть доступные сервисы.
цель: найти точку входа, ftp или smb
```
sudo nmap -sC -sV -p- 10.129.140.247 -T4
```
```
21/tcp   open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: TIMEOUT
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.143.111
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
| http-robots.txt: 2 disallowed entries 
|_/ /openemr-5_0_1_3 
2222/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 29:42:69:14:9e:ca:d9:17:98:8c:27:72:3a:cd:a9:23 (RSA)
|   256 9b:d1:65:07:51:08:00:61:98:de:95:ed:3a:e3:81:1c (ECDSA)
|_  256 12:65:1b:61:cf:4d:e5:75:fe:f4:e8:d4:6e:10:2a:f6 (ED25519)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
Есть точка входа на ftp 21 как анонимус и ssh2222 это не стандартный порт для ssh
подключаюсь по ftp
```
ftp anonymous@10.129.140.247
```
Включен пассивный мод
```
ftp> ls
229 Entering Extended Passive Mode (|||48263|)
```
Отключается следующей командой:
```
ftp> passive off
Passive mode: off; fallback to active mode: off.
```
Смотрю еще раз вижу папку Pub перехожу туда
```
ftp> ls
drwxr-xr-x    2 ftp      ftp          4096 Aug 17  2019 pub
ftp> cd pub
250 Directory successfully changed.
```
Отлично в ней лежит какой-то тектовый файл для Митча, скачаю его и просмотрю возможно в ней ценная инфа
```
ftp> ls
-rw-r--r--    1 ftp      ftp           166 Aug 17  2019 ForMitch.txt
ftp> get ForMitch.txt
226 Transfer complete.
```
Отлично Митча отчитывают за простой пароль, значит он станет целью для брутфорса и точкой входа в систему
```
cat ForMitch.txt                             
Dammit man... you'te the worst dev i've seen. You set the same pass for the system user, and the password is so weak... i cracked it in seconds. Gosh... what a mess!
```



