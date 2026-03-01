# 🔹 Глава 4. Shell и окружение — QA Cheatsheet

---

## 4.1 Bash + переменные
~~~bash
echo "$SHELL"
echo "$0"
bash --version
echo "$PATH"
env | grep HOME
~~~

---

## 4.2 Export (дочерним процессам)
~~~bash
MYVAR="hello"
export MYVAR
bash -lc 'echo $MYVAR'
~~~

---

## 4.3 История команд
~~~bash
history
!!  
!grep
Ctrl+R
~~~

---

## 4.4 Aliases
~~~bash
alias ll='ls -lahF'
alias
echo "alias ll='ls -lahF'" >> ~/.bashrc
source ~/.bashrc
~~~

---

## 4.5 Диагностика команд
~~~bash
type -a ls
which ls
command -V ls
~~~

