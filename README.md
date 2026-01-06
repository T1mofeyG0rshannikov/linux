# 🚀 Справочник по деплою и настройке серверов (Linux)

Этот документ создан как личная инструкция для быстрого деплоя проектов, настройки сервисов и SSL-сертификатов.  

---

## 📦 Установка Python и виртуального окружения

```bash
sudo apt update
sudo add-apt-repository ppa:deadsnakes/ppa -y
sudo apt update
sudo apt install python3.11
sudo apt-get install python3.11-venv
```

Создание окружения и установка зависимостей:

```bash
cd /home/project_name
python3.11 -m venv env
source env/bin/activate
pip install -r requirements.txt
```

---

## ⚙️ Настройка systemd сервисов

Файлы сервисов находятся в `/etc/systemd/system`.

### Gunicorn
```ini
[Unit]
Description=Gunicorn WSGI Server

[Service]
ExecStart=/home/project_name/project_src/up_gunicorn.sh
Restart=always

[Install]
WantedBy=multi-user.target
```

## Примеры файлов для systemd сервисов

### up_uvicorn.sh

```
#!/bin/bash
source /home/project/env/bin/activate
cd /home/project/src
exec uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

### up_gunicorn.sh

```
#!/bin/bash
source /home/project/env/bin/activate
cd /home/project/src
exec gunicorn --bind 0.0.0.0:8000 core.wsgi:application
```

### up_celery.sh

```
#!/bin/bash
source /home/project/env/bin/activate
cd /home/project/src
exec celery -A app.tasks worker -l INFO --pool=solo
```

### up_celery_beat.sh

```
#!/bin/bash
source /home/project/env/bin/activate
cd /home/project/src
exec celery -A app.tasks beat -l INFO
```

## Установка Reboot скрипта

```
cd /etc
```
в файле crontab в конце добавляем
```
@reboot root sh /home/up_server.sh
```

### Общие команды
```bash
chmod -R 755 /home/project_name/

sudo systemctl daemon-reload
sudo systemctl start gunicorn
```

---

## 📜 Логи сервисов

Просмотр последних 100 строк лога Gunicorn:
```bash
journalctl -u gunicorn --no-pager -n 100
```

---

## 🔒 Настройка SSL (Certbot + Nginx)

Установка Certbot:
```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

Выпуск сертификата для домена:
```bash
sudo certbot --nginx -d zagran.anketus.ru
```

---

## ✅ Полезные команды

Проверка автоматического продления SSL:
```bash
sudo certbot renew --dry-run
```

---
