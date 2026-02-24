# 02 — Pipes + redirects (QA mini-cheatsheet)

Цель: быстро собирать команды вида “взял вывод → отфильтровал → преобразовал → посчитал → взял топ”.

## Модель потока
- `stdin` — вход.
- `stdout` — обычный вывод.
- `stderr` — ошибки.
- Pipe `|` передаёт **stdout** команды слева в **stdin** команды справа.

## Pipes: самые частые блоки

### Фильтрация (grep)
- Найти строки по шаблону:
  cmd | grep "pattern"
- Без учёта регистра:
  cmd | grep -i "pattern"
- Исключить строки (инверсия):
  cmd | grep -v "pattern"

### Ограничение вывода (head/tail)
- Первые N строк:
  cmd | head -n 20
- Последние N строк:
  cmd | tail -n 20
- “Взять конкретную строку k” (пример: 4-ю):
  cmd | head -n 4 | tail -n 1

### Извлечение полей (awk)
- Первое поле:
  cmd | awk '{print $1}'
- Последнее поле:
  cmd | awk '{print $NF}'

### Подсчёт частот (sort + uniq)
Важно: `uniq` считает только соседние одинаковые строки → почти всегда нужен `sort` перед `uniq -c`.

- Посчитать частоты:
  cmd | sort | uniq -c
- Топ по частоте:
  cmd | sort | uniq -c | sort -nr | head -n 10

## Redirects: stdout/stderr в файлы

### Запись stdout
- Перезаписать файл:
  cmd > out.txt
- Дописать в конец:
  cmd >> out.txt

### Запись stderr
- Только ошибки:
  cmd 2> err.txt

### Слить stdout + stderr вместе
- В один файл:
  cmd > all.txt 2>&1
Порядок важен: сначала задаём stdout, потом “приклеиваем” stderr к stdout.

### Выкинуть шум в /dev/null
- Выкинуть только ошибки:
  cmd 2>/dev/null
- Выкинуть вообще всё:
  cmd >/dev/null 2>&1

## Мини‑практика (5 задач)

### 1) Последняя строка с error
Дано:
printf "ok\nerror: one\nok\nerror: two\nok\n"
Нужно вывести только `error: two`.

### 2) Посчитать частоты и отсортировать по убыванию
Дано:
printf "a\nb\na\na\nb\nc\n"
Нужно получить топ: `3 a`, `2 b`, `1 c` (в этом порядке).

### 3) Топ Invalid user (через awk)
Дано:
printf "Feb 19 sshd Invalid user admin\nFeb 19 sshd Invalid user root\nFeb 19 sshd Invalid user admin\n"
Нужно вывести `admin 2`, `root 1` (с подсчётом, по убыванию).

### 4) Найти error (без регистра), но исключить timeout
Дано:
printf "INFO started\nWARN disk\nERROR failed\nerror timeout\n"
Нужно вывести только `ERROR failed`.

### 5) Проверка redirect: stdout отдельно от stderr
Сделай так, чтобы stdout попал в `out.txt`, а stderr в `err.txt`:
ls /tmp /no_such_dir
Подсказка: используй `>` и `2>`, затем посмотри файлы через `cat out.txt` и `cat err.txt`.

