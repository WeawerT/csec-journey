# 🖥️ Отчет по машине: SimpleCTF

**Дата:** 2026-05-04
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

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

Порты 139 и 445 это samba, значит запускаю ```enum4linux``` для поиска кредов 

Есть юзернеймы, так же виден доступ anonumous без пароля он станет 1 точкой входа для эскалации
<img width="540" height="79" alt="Снимок экрана 2026-05-04 в 07 15 55" src="https://github.com/user-attachments/assets/6afc7089-b559-4b14-8044-026a63d80cfe" />

