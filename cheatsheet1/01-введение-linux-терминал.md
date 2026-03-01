# 🔹 Глава 1. Введение в Linux и терминал — QA Cheatsheet

---

## Shell
~~~bash
whoami
id
pwd
tty
echo "$SHELL"
echo "$0"
~~~

---

## Терминалы и сессии  
~~~bash
who
w
~~~

---

## Переменные окружения
~~~bash
env | grep PATH
printenv HOME
echo "$PATH"
~~~

---

## Редиректы + пайпы
~~~bash
cmd > out.txt
cmd 2> err.txt
cmd > all.txt 2>&1
grep ssh /var/log/auth.log | tail -20
~~~
