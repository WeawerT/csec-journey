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



```
showmount -e 10.129.39.79
```

```
sudo mkdir -p /mnt/onboarding
sudo mount -t nfs -o vers=3,nolock 10.129.39.79:/srv/nfs/onboarding /mnt/onboarding
ls -la /mnt/onboarding
# -rw-r--r-- 1 root root 1751 ... New_Employee_Access.pdf
```
