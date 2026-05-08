# 🖥️ Отчет по машине: tools rus

**Дата:** 2026-05-06
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

---
## Использованные интсрументы 
dirsearch | nmap | enum4linux | hydra

---
## 📡 1. Разведка 
Начну с главное страницы, цель просмотр на возможные уязвимости, комменты или креды 
<img width="829" height="227" alt="Снимок экрана 2026-05-06 в 07 14 55" src="https://github.com/user-attachments/assets/4be4a47d-629e-47b0-afae-9beaaf8c9958" />

```
curl http://10.129.187.90                                                                                           
<center>
</br><img width=350 src="toysrus.png">
<p>Unfortunately, <b>ToolsRUs</b> is down for upgrades. Other parts of the website is still functional...</p>
</center>
```
ничего не найдено, переключаюсь на поиск скрытых дирректорий, цель поиск путей и кредов 
```
dirsearch -u http://10.129.187.90 
```
<img width="810" height="529" alt="Снимок экрана 2026-05-06 в 07 18 21" src="https://github.com/user-attachments/assets/49ded1a8-b791-4562-9e11-714000ce6616" />
есть несколько путей, они требуют авторизацию
Запускаю gobuster с более большим словарем что бы исключить существование других дирректорий

<img width="705" height="448" alt="Снимок экрана 2026-05-06 в 07 34 57" src="https://github.com/user-attachments/assets/8441671a-f8d9-4b02-9736-ada54435d800" />
И gobuser нашел еще один путь 

```
curl http://10.129.187.90/guidelines/                                                                    
Hey <b>bob</b>, did you update that TomCat server?

```
```
hydra -l bob -P /usr/share/wordlists/rockyou.txt 10.129.187.90 -s 80 http-get /protected/

```
