# 🖥️ Отчет по машине: Responder
**Дата:** 2026-06-26
**Платформа:** HackTheBox
**Сложность:** Easy
**Статус:** In progress

---
Под скриптом, на простые вопросы буду отвечать без пояснений 
---
## 1. Разведка 

### Nmap
Запуск сканирования портов, чтобы увидеть доступные сервисы и их версии 
<img width="625" height="233" alt="Снимок экрана 2026-06-26 в 06 40 25" src="https://github.com/user-attachments/assets/321d799b-ea8f-457d-b941-8d82e8edc7b2" />
Два открытых порта, пеерключусь на 80.

### curl 
<img width="536" height="105" alt="Снимок экрана 2026-06-26 в 06 40 40" src="https://github.com/user-attachments/assets/a57f8aad-3a14-419a-b3b1-b17b6a84fb17" /> 

task 1: When visiting the web service using the IP address, what is the domain that we are being redirected to?
answer: unika.htb
 
у сайта редирект, нужно добавить его в /etc/hosts

~~~
echo '<IP target> domain.htb' | sudo tee /etc/hosts
~~~

task 2: Which scripting language is being used on the server to generate webpages?
answer: php

task 3: What is the name of the URL parameter which is used to load different language versions of the webpage?
answer: page
<img width="324" height="28" alt="Снимок экрана 2026-06-26 в 06 52 48" src="https://github.com/user-attachments/assets/05719ef2-3b73-41f0-be21-a97483f8f001" />

task 4: Which of the following values for the page parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "mimikatz.exe"
answer



