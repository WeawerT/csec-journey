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
