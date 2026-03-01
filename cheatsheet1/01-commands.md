# Cheatsheet 01: Shell + Commands — QA Linux

Цель: базовый shell, окружение, простые потоки для диагностики.

---

## 1) Кто я и где я
~~~bash
whoami pwd echo "$HOME" echo "$SHELL" echo "$0" tty
~~~

---

## 2) Переменные окружения
~~~bash
env | grep PATH
printenv HOME
echo "$PATH"
~~~

---

## 3) Редиректы + базовые пайпы
~~~bash
cmd > out.txt 2> err.txt > all.txt 2>&1
grep ssh /var/log/auth.log | tail -20
~~~

