# Cheatsheet 03: History + Aliases + Jobs — QA

Цель: экономить время, повторять команды, управлять фоном.

---

## 1) History Expansion
~~~bash
**!!** (последняя) **!\\*** (аргументы) **!\\$** (последний arg) **Ctrl+R** (поиск)
~~~

**Пример:** `cat file && head -5 !\\$`

---

## 2) Диагностика команд
~~~bash
**type -a** ls     # alias/function/binary
**which** ls       # путь
**command -V** ls
~~~

---

## 3) Aliases
~~~bash
alias ll='ls -lahF'
echo "alias ll='ls -lahF'" >> ~/.bashrc
source ~/.bashrc
~~~

---

## 4) Jobs (фон)
~~~bash
sleep 60 **&**    **jobs**    **fg %1**    **Ctrl+Z**    **bg %1**    **kill %1**
