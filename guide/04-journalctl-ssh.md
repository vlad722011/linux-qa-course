# Guide 04: journalctl + SSH-диагностика (Ubuntu 24.04) — QA-практика

## Цель главы
После этой главы ты должен уметь:
- Быстро находить SSH-события: через /var/log/auth.log и через journalctl.
- Проверять реальное имя systemd-юнита SSH на конкретном сервере.
- Снимать “артефакты” логов для тикета/CI: raw + выжимка.

---

## 1) Где смотреть SSH-логи на Ubuntu
На Debian/Ubuntu часто используется файл:
- `/var/log/auth.log` — события аутентификации (включая ssh).

Также на системах с systemd можно смотреть журнал:
- `journalctl` (фильтрация по юниту/времени).

Если файла auth.log нет или он пустой — journalctl обычно поможет. [web:91]

---

## 2) Найди правильный юнит SSH на своём сервере
В разных дистрибутивах имя юнита отличается.
На Ubuntu 24.04 часто используется `ssh.service` (а `sshd.service` может отсутствовать или быть ссылкой/неактивным). [web:42]

Проверка:
~~~bash
systemctl list-units --type=service | grep -i ssh
systemctl list-units --type=socket  | grep -i ssh
~~~

Статус (попробуй оба варианта, какой есть):
~~~bash
systemctl status ssh --no-pager
systemctl status sshd --no-pager
~~~

---

## 3) journalctl: базовые фильтры
Показать последние 100 строк:
~~~bash
journalctl -n 100 --no-pager
~~~

Следить в реальном времени (как tail -f):
~~~bash
journalctl -f
~~~

Логи конкретного юнита (SSH):
~~~bash
journalctl -u ssh --no-pager
~~~

Фильтр по времени:
~~~bash
journalctl --since "2 hours ago" --no-pager
~~~
Опции `--since/--until` описаны в man journalctl. [web:108]


---

## 4) Практика: “снять диагностику SSH за последние 2 часа”
### 4.1 Raw-лог (контекст)
~~~bash
journalctl -u ssh --since "2 hours ago" --no-pager > ssh_journal_last2h_raw.log
~~~

### 4.2 Выжимка (быстрый просмотр)
~~~bash
journalctl -u ssh --since "2 hours ago" --no-pager \
  | grep -iE "failed|accepted|disconnect|error" \
  > ssh_journal_last2h_filtered.log
~~~

---

## 5) Практика: через /var/log/auth.log (если файл есть)
Raw:
~~~bash
sudo tail -n 400 /var/log/auth.log > auth_last400_raw.log
~~~

Выжимка:
~~~bash
sudo tail -n 400 /var/log/auth.log \
  | grep -iE "sshd|failed|accepted|disconnect|error" \
  > auth_last400_filtered.log
~~~

---

## 6) QA-инцидент: “после изменения sshd_config пропал доступ”
Правило №1: никогда не закрывай текущую SSH-сессию, пока не проверил вход во второй вкладке.

Сценарий проверки (безопасный):
1) Открыть вторую SSH-сессию.
2) Проверить имя юнита и статус SSH:
~~~bash
systemctl status ssh --no-pager
systemctl status sshd --no-pager
~~~
3) Посмотреть последние ошибки запуска:
~~~bash
journalctl -u ssh -n 100 --no-pager
~~~

---

## 7) Задания (сдача)
1) На твоём сервере выясни: как называется SSH-юнит (ssh или sshd) и есть ли ssh.socket.
Команды (вставь вывод):
~~~bash
systemctl list-units --type=service | grep -i ssh
systemctl list-units --type=socket  | grep -i ssh
~~~

2) Сними логи SSH за последний час в 2 файла:
- raw (всё подряд),
- filtered (только failed/accepted/error).
