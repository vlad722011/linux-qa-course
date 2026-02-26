# Cheatsheet 01: Shell + Streams (stdin/stdout/stderr) — QA Linux

Цель: быстро понимать shell и потоки, чтобы диагностировать проблемы на Ubuntu-сервере по SSH.

---

## 1) Терминал, shell, сессия
- Терминал — интерфейс (окно/ssh-клиент), через который ты общаешься с shell.
- Shell (обычно bash) — запускает процессы и связывает потоки.
- Каждое SSH-подключение = отдельная сессия.

Быстрые проверки:
~~~bash
whoami
id
pwd
tty
echo "$SHELL"
echo "$0"
~~~

---

## 2) Стандартные потоки
- stdin (0) — ввод
- stdout (1) — обычный вывод
- stderr (2) — ошибки

---

## 3) Редиректы (самое нужное)
~~~bash
cmd > out.txt
cmd >> out.txt
cmd 2> err.txt
cmd > all.txt 2>&1
cmd &> all.txt
~~~

---

## 4) Пайпы (|)
Пайп соединяет stdout одной команды со stdin другой.

~~~bash
grep ssh /var/log/auth.log | tail -n 20
~~~

---

## 5) QA-паттерны (копипаст)
Последние SSH-события:
~~~bash
sudo tail -n 200 /var/log/auth.log | grep -i ssh | tail -n 50
~~~

Stdout и stderr отдельно:
~~~bash
some_command > out.log 2> err.log
~~~

Оба потока в один файл:
~~~bash
some_command > all.log 2>&1
~~~

---

## 6) Мини-проверка (1 мин)
~~~bash
( echo "OUT"; echo "ERR" >&2 ) > out.txt 2>&1
cat out.txt
~~~
