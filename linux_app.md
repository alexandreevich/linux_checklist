# Linux работа на сервере 

## Напоминалка по командам

---

### 📏 Размер файлов и папок
```
du -sh *              # Показать размер всех директорий и файлов в текущей папке
du -sh /var/log       # Узнать размер конкретной директории
df -h                 # Общий объём и свободное место на дисках
ls -lh                # Размер файлов в читаемом виде
find / -type f -size +500M 2>/dev/null   # Найти файлы больше 500MB
```

### Пользователи и права
```
sudo adduser devuser                # Создать пользователя с домашней директорией
sudo adduser --no-create-home app   # Создать пользователя без домашней директории (сервисный)
sudo passwd devuser                 # Назначение пароля
sudo usermod -aG group devuser       # Добавление в группу
sudo chown username:groupname file.txt     # Назначить владельца и группу
sudo chown -R appuser:appgroup /opt/app    # Рекурсивно на директорию
chmod 755 file.sh          # Владельцу — все, остальным — чтение/выполнение
chmod 644 config.yaml      # Владельцу — запись, остальным — только чтение
chmod -R 770 /opt/app      # Полные права владельцу и группе
```

### Логи и диагностика
```
journalctl -xe             # Последние ошибки systemd
journalctl -u nginx        # Логи конкретного юнита
tail -n 100 /var/log/syslog     # Последние 100 строк системного лога
tail -f /var/log/app.log        # Смотреть лог приложения в реальном времени
grep -i "error" /var/log/syslog     # Найти ошибки в логах
```

### Работа с архивами
```
tar -czvf backup.tar.gz /opt/app/        # Архивировать папку
tar -xzvf backup.tar.gz -C /tmp/         # Распаковать архив
zip -r app.zip /opt/app                  # Упаковать в zip
unzip app.zip -d /tmp/app                # Распаковать zip
```

### Полезные команды при отладке
```
env | sort                    # Посмотреть переменные окружения
which python3                 # Узнать, откуда запускается программа
cat /etc/os-release           # Проверить версию системы
uptime                        # Время работы и нагрузка
free -h                       # Проверить использование памяти
dmesg | tail -20              # Последние системные события

```



