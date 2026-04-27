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

видит только 80 порт проверю иначе
```
sudo nmap -sU --top-ports 20 -v 10.129.132.16
```
<img width="314" height="350" alt="Снимок экрана 2026-04-27 в 15 42 16" src="https://github.com/user-attachments/assets/e01d6ca9-8328-4d26-b1dc-515ea86e1b32" />

### Порт 80 Стандартная страница сервера Ubuntu
проверю какие папки есть 
dirsearch -u http://ip
