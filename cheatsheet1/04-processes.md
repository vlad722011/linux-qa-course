# Cheatsheet 04: Processes (ps/kill/jobs) — QA

Цель: находить/убивать процессы, статусы (R/S/Z/D), фон.

---

## 1) Список процессов
~~~bash
**ps aux** (все) **ps -ef** (дерево) **pgrep** nginx **pkill** nginx
**top/htop** (q=выход k=kill)
~~~

---

## 2) Job Control
~~~bash
sleep 300 **&**     **jobs**     **fg %1**     **Ctrl+Z**     **bg %1**
~~~

---

## 3) Kill сигналы
~~~bash
**kill PID** (TERM) **kill -9 PID** (KILL) **kill -STOP/CONT PID**
kill -l (список сигналов)
~~~

**QA:** TERM→KILL, `jobs` — только свои задачи shell.
