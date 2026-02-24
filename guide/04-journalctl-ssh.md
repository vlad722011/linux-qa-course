# 04 — journalctl для SSH (qa-расследования)

Цель: быстро находить в systemd‑журнале события SSH (успешные/неуспешные логины, “invalid user”, ошибки сервиса), ограничивать по времени и следить в реальном времени.

## 1) Как найти “правильный” unit
В разных дистрибутивах SSH сервис может называться `ssh.service` или `sshd.service`, поэтому если `-u ssh` пусто — проверь второй вариант. [web:40]

Полезные проверки:
- `systemctl status ssh` или `systemctl status sshd`
- `journalctl -u ssh -n 20` и `journalctl -u sshd -n 20`

## 2) Последние строки и “follow”
- Последние N строк:
`journalctl -u ssh -n 100` [web:40]
- Следить в реальном времени:
`journalctl -u ssh -f` [web:40]
- Показать новые сначала (reverse):
`journalctl -u ssh -r -n 50` [web:267]

## 3) Фильтр по времени (самое нужное)
- С начала дня:
`journalctl -u ssh --since "today"` [web:269]
- За последний час:
`journalctl -u ssh --since "1 hour ago"` [web:40]

Комбинация “время + ключевые слова” для быстрых аудитов:
`journalctl -u ssh --since "1 hour ago" | grep "Failed password"` [web:40]

## 4) Типовые запросы по SSH
- Неуспешные попытки:
`journalctl -u ssh --since "today" | grep "Failed password"` [web:40]
- Успешные входы:
`journalctl -u ssh --since "today" | grep "Accepted password"` [web:40]
- Поиск “auth/failed/error” без регистра (как универсальный фильтр):
`journalctl -u ssh --since today | grep -i "authentication\|failed\|error"` [web:273]

## 5) Быстрые “топы” из логов (пайпы)
Топ “Invalid user” логинов за сегодня:
`journalctl -u ssh --since "today" | grep "Invalid user" | awk '{print $(NF-5)}' | sort | uniq -c | sort -nr | head` [web:273]

Топ IP по “Failed password” (подстрой awk под формат строки на твоём хосте):
`journalctl -u ssh --since "today" | grep "Failed password" | awk '{print $(NF-3)}' | sort | uniq -c | sort -nr | head` [web:40]

## 6) Мини‑контроль (что ты должен уметь)
1) Показать последние 50 строк SSH, новые сверху. [web:267]  
2) Найти все “Failed password” за последний час. [web:40]  
3) Запустить просмотр SSH логов в реальном времени. [web:40]  
4) Если `journalctl -u ssh` пустой — проверить альтернативный unit (`sshd`). [web:40]
