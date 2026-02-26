# Guide 03: Пайпы и обработка текста — QA-диагностика логов

## Цель главы
После этой главы ты должен уметь:
- Читать логи быстро: less/tail/grep.
- Собирать пайплайны: `grep | awk | sort | uniq -c | sort -nr`.
- Делать отчёты в файлы (артефакты) для тикета/CI.

---

## 1) Главная идея пайпов
Пайп `|` соединяет stdout команды слева со stdin команды справа.

То есть это не “магия текста”, а конвейер процессов.

Пример:
~~~bash
grep -i ssh /var/log/auth.log | tail -n 20
~~~

---

## 2) Базовые команды для текста (минимум)
- `less file` — постранично (в логах лучше, чем cat)
- `head -n 20 file` — первые строки
- `tail -n 50 file` — последние строки
- `tail -f file` — следить за логом
- `grep -i "pattern" file` — поиск
- `wc -l` — посчитать строки

Практика:
~~~bash
cd /var/log
ls -lah | head
sudo tail -n 50 /var/log/auth.log
sudo grep -i ssh /var/log/auth.log | tail -n 20
~~~

---

## 3) Правило “проверки глазами” перед awk
Перед тем как парсить `awk`, сначала посмотри 20–50 строк:
~~~bash
sudo tail -n 50 /var/log/auth.log
~~~
Потом уже выбирай поля.


---

## 4) Реальный QA-сценарий: “кто брутит SSH”
Сначала убедись, что строки есть:
~~~bash
sudo grep "Failed password" /var/log/auth.log | tail -n 5
~~~

Быстрый отчёт “топ IP”:
~~~bash
sudo grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3)}' \
  | sort | uniq -c | sort -nr | head
~~~

Пояснение:
- В awk `NF` — число полей в строке, `$NF` — последнее поле, `$(NF-3)` — поле “за несколько позиций до конца”. [web:103]
- Но формат auth.log может отличаться, поэтому поле с IP нужно подтверждать глазами (см. правило выше).

---

## 5) Комбинации: сохранить результат и отделить ошибки
Сохранить результат в файл:
~~~bash
sudo grep "Failed password" /var/log/auth.log | tail -n 200 > failed_ssh_last200.log
~~~

Сохранить stdout и stderr вместе в файл (важен порядок редиректов):
~~~bash
some_command > all.log 2>&1
~~~
Bash обрабатывает редиректы слева направо; порядок значим. [web:96]

---

## 6) Практика на VPS (15–30 минут)
1) Сделай файл `failed_ssh_last50.log`:
~~~bash
sudo grep "Failed" /var/log/auth.log | tail -n 50 > failed_ssh_last50.log
~~~

2) Сделай файл `ssh_top_ip.txt` (топ-10 IP по Failed password).

3) Сделай “артефакт”: запусти `ls /tmp /no_such_dir` так, чтобы всё попало в `all.log`.

---

## 7) Мини-инцидент (как на работе)
Симптом: “после деплоя тесты начали падать по таймаутам, подозрение на SSH/доступ”.

Твои действия:
1) Снять последние 200 строк auth.log в raw-файл:
~~~bash
sudo tail -n 200 /var/log/auth.log > auth_last200_raw.log
~~~
2) Снять выжимку “Failed/ssh” в отдельный файл:
~~~bash
sudo tail -n 200 /var/log/auth.log | grep -i ssh | grep -i failed > auth_last200_failed_ssh.log
~~~
3) Приложить оба файла в тикет: raw даёт контекст, выжимка ускоряет поиск причины.
