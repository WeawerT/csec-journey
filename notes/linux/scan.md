# ========== ПОСЛЕ ВХОДА (всегда в этом порядке) ==========

# 1. Кто я и что могу
whoami
id
sudo -l


# 2. SUID-бинарники (быстрый просмотр)
find / -perm -4000 -type f 2>/dev/null | head -20

# 3. Кроны (быстрый просмотр)
cat /etc/crontab 2>/dev/null
ls -la /etc/cron* 2>/dev/null | head -20

# 4. Запись в системные файлы
ls -la /etc/passwd /etc/shadow /etc/sudoers

# 5. Версия ядра (для поиска эксплойтов)
uname -a

# ========== ЕСЛИ НИЧЕГО НЕ НАШЁЛ ==========

# 6. Запуск LinPEAS (если есть)
./linpeas.sh > /tmp/lin.txt 2>&1
less -R /tmp/lin.txt

# 7. Поиск по ключевым словам в выводе LinPEAS
grep -i "suid\|sudo\|cron\|writable\|cap\|cve" /tmp/lin.txt

# ========== ПОСЛЕДНИЙ ШАГ (если всё глухо) ==========

# 8. Поиск эксплойта по ядру
searchsploit $(uname -r | cut -d'-' -f1)
