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
```
curl http://10.129.187.90                                                                                           
<center>
</br><img width=350 src="toysrus.png">
<p>Unfortunately, <b>ToolsRUs</b> is down for upgrades. Other parts of the website is still functional...</p>
</center>
```
```
dirsearch -u http://10.129.187.90 
```
