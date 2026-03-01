# 🔹 Глава 1. Введение в Linux и терминал — QA Cheatsheet

---

## 1.1 Почему Linux
Linux — свободная ОС для серверов, VPS, CI/CD. Для QA: диагностика, контроль процессов.

---

## 1.2 Shell vs GUI  
~~~bash
echo "$SHELL"
echo "$0"
~~~

---

## 1.3 Терминалы и сессии
~~~bash
who
w
tty
~~~

---

## 1.4 Потоки: stdin/stdout/stderr
~~~bash
echo "hello" > output.txt
echo "error" 2> error.log  
echo "both" &> both.log
~~~

---

## 1.5 Первые команды
~~~bash
pwd
cd /home
ls -lah
echo "Hello Linux"
man ls
~~~

---

## 1.6 История + Alias
~~~bash
history
!!  
alias ll='ls -lah'
~~~

---

## 1.7 Практический кейс QA
~~~bash
ssh dev@vps_ip
env | grep HOME
cat /var/log/syslog | grep ssh | tail -n 20
~~~
