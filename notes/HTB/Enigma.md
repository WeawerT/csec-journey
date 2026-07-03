# 🖥️ Отчет по машине: Enigma
**Дата:** 2026-07-02
**Платформа:** HackTheBox
**Сложность:** Easy
**Статус:** In progress


## 1. Разведка 

Добавить домен в etc/hosts

```
echo "10.129.39.79 enigma.htb" | sudo tee -a /etc/hosts
```

### Nmap
Запуск сканирования портов, чтобы увидеть доступные сервисы и их версии 

<img width="686" height="599" alt="Снимок экрана — 2026-07-03 в 07 41 40" src="https://github.com/user-attachments/assets/1ce4cc53-7090-4e0f-bbb0-cfaf7da629c6" />
<img width="685" height="324" alt="Снимок экрана — 2026-07-03 в 07 41 52" src="https://github.com/user-attachments/assets/99ac5dd4-2d0a-425b-9d5a-e28bf36e577c" />

Открыта куча портов, но интерсные здесь это почтовые стеки, возможно подключиться анонимно 

```
showmount -e 10.129.39.79
```
<img width="248" height="75" alt="Снимок экрана — 2026-07-03 в 07 45 36" src="https://github.com/user-attachments/assets/3dfddc76-9855-43b2-919f-327658aaf822" />

* означает что можно без пароля смотреть и монтировать файл 

```
sudo mkdir -p /mnt/onboarding
sudo mount -t nfs -o vers=3,nolock 10.129.39.79:/srv/nfs/onboarding /mnt/onboarding
ls -la /mnt/onboarding
# -rw-r--r-- 1 root root 1751 ... New_Employee_Access.pdf
```
это пдф, с кредами 

<img width="738" height="628" alt="Снимок экрана — 2026-07-03 в 07 58 01" src="https://github.com/user-attachments/assets/621f07c2-d9cc-48ef-b23d-3151b9f4357d" />

надо добавить новый субдомен к enigma через sudo nano 

здесь можно увидеть еще 1го пользователя Sarah

<img width="456" height="537" alt="Снимок экрана — 2026-07-03 в 08 00 14" src="https://github.com/user-attachments/assets/7111fdfa-4820-41ae-a9bd-c8b5605bb70f" />

стоит попробывать войти через нее с этим же паролем 

<img width="448" height="326" alt="Снимок экрана — 2026-07-03 в 08 02 35" src="https://github.com/user-attachments/assets/e771ffdd-2e40-4baa-9078-a579b5576e1e" />

отлично есть доступ, а так же новый субдомен, по аналогии добавляем его в hosts 

URL: http://support_001.enigma.htb
Username: admin
Password: Ne3s4rtars78s

