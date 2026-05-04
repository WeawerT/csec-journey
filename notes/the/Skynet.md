# 🖥️ Отчет по машине: SimpleCTF

**Дата:** 2026-04-30
**Платформа:** TryHackMe
**Сложность:** Easy
**Статус:** In progress

---

## 📡 1. Разведка 

```
curl http://10.129.145.218                          
<!DOCTYPE html>
<html>
        <head>
                <link rel="stylesheet" type="text/css" href="style.css">
                <link rel="shortcut icon" type="image/png" href="favicon.ico"/>
                <title>Skynet</title>
        </head>
        <body>
                <div>
                        <img src="image.png"/>
                        <form name="skynet" action="#" method="POST"><br>
                                <input type="search" class="search"><br>
                                <input type="submit" class="button" name="submit" value="Skynet Search">
                                <input type="submit" class="button" name="lucky" value="I'm Feeling Lucky">
                        </form>
                </div>
        </body>
</html>

```
