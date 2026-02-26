# Cheatsheet 03: Shell и окружение (env, PATH, export) — QA

Цель: быстро ориентироваться в переменных окружения, PATH, и понимать, почему “в одном терминале работает, а в другом/в CI — нет”.

---

## 1) Кто я и где я
~~~bash
whoami
pwd
echo "$HOME"
echo "$SHELL"
echo "$0"
~~~

---

## 2) Переменные окружения (env)
Показать всё окружение:
~~~bash
env
printenv
~~~

Показать конкретные:
~~~bash
printenv HOME
printenv PATH
echo "$PATH"
~~~

---

## 3) Локальная переменная vs export
Локальная (только в текущем shell):
~~~bash
MYVAR="hello"
echo "$MYVAR"
bash -lc 'echo "child: $MYVAR"'
~~~

Экспорт (видно дочерним процессам):
~~~bash
export MYVAR="hello"
bash -lc 'echo "child: $MYVAR"'
~~~
Смысл: `export` помечает переменную для передачи дочерним процессам. [web:142]

---

## 4) PATH (почему команда “не найдена”)
Посмотреть:
~~~bash
echo "$PATH"
~~~

Проверить, откуда берётся команда:
~~~bash
which bash
type -a bash
~~~

---

## 5) Быстрые QA-паттерны
Снять окружение в файл (артефакт CI):
~~~bash
env | sort > env_snapshot.txt
~~~

Проверить, что переменная есть в процессе:
~~~bash
export APP_ENV="staging"
bash -lc 'env | grep APP_ENV'
~~~
