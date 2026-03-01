# Guide 03: History, Aliases, Job Control — QA

Цель: автоматизировать повторяющиеся команды, управлять фоновыми задачами, диагностировать "что выполняется".

---

## 1) History Expansion — экономия времени
**Синтаксис:**
~~~bash
**!!**              # повторить **последнюю** команду  
**!$\**             # **последний аргумент**
**!* **             # **все аргументы**
**Ctrl+R**          # интерактивный поиск
~~~

**Практика:**
~~~bash
cat /etc/passwd
head -5 **!$\**        # → head -5 /etc/passwd
!! | grep root         # повтори + фильтр
~~~

---

## 2) Aliases (псевдонимы)
**Где задавать:** `~/.bashrc` (читается при старте интерактивного shell).

~~~bash
echo "alias ll='ls -lah'" >> ~/.bashrc
echo "alias grep='grep --color=auto'" >> ~/.bashrc
source ~/.bashrc
~~~

**Диагностика:**
~~~bash
**type -a** ll         # показывает alias + binary
**command -V** ll      # тип команды
**which** ll           # только бинарник
~~~

---

## 3) Job Control
**Жизненный цикл задачи:**
~~~bash
sleep 300 **&**        # [1] 12345 — в фон
**jobs**               # [1]+  Running  sleep 300 &
**Ctrl+Z**             # [1]+  Stopped   sleep 300
**bg %1**              # [1]+  Running   sleep 300 &
**kill %1**            # завершить
**jobs**               # [1]+  Done      sleep 300
~~~

**QA-tip:** `%1` — номер job, `%sleep` — по имени. `jobs` видит **только свои** задачи shell'а.
