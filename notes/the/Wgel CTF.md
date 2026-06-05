# 🖥️ Отчет по машине: Wgel CTF

**Дата:** 2026-06-05
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** Done

---

## Использованные интсрументы 
dirsearch | nmap | enum4linux | hydra

---

## 📡 1. Разведка 

### Веб
Проверил сайт, выдает дефолтную страницу сервера убунту
комментариев тоже нет (стоит проверить пути)

### DIrsearch

```
dirsearch -u http://10.113.153.249 
```
<img width="683" height="500" alt="Снимок экрана 2026-06-05 в 07 35 24" src="https://github.com/user-attachments/assets/3f79c831-4c22-4fb7-a900-940fc5d3d610" />
Нашел интересный путь

```
dirsearch -u http://10.113.153.249/sitemap
```
<img width="683" height="631" alt="Снимок экрана 2026-06-05 в 07 53 31" src="https://github.com/user-attachments/assets/16622c20-99ca-46c0-ae6e-bc07d6303f68" />

Нашел id_rsa 



