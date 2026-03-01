# CheatSheet 01: Shell и потоки (stdin/stdout/stderr) — быстрый взгляд (QA)

---

## Терминал и shell
- Терминал = окно/SSH-клиент/IDE  
- Shell (bash) = читает команды, создаёт процессы, управляет stdin/stdout/stderr, возвращает exit code  
- Сессия = отдельное подключение с каталогом, переменными и процессами

---

## Основные команды сессии
~~~bash
whoami       # имя пользователя
pwd          # текущий каталог
tty          # терминальное устройство
echo "$SHELL" # используемый shell
ps           # процессы текущей сессии
who / w      # кто подключён, что делает
~~~

---

## Потоки данных
| Поток  | Описание |
|--------|----------|
| stdin  | вход (клавиатура/другая команда) |
| stdout | обычный вывод |
| stderr | ошибки и диагностика |

**QA:** stderr отдельно → удобнее искать ошибки в CI

---

## Редиректы
~~~bash
# stdout → файл
echo "text" > out.txt

# stderr → файл
ls /no_such_dir 2> err.txt

# stdout+stderr → один файл
ls /tmp /no_such_dir > all.txt 2>&1

# сокращение bash
ls /tmp /no_such_dir &> all.txt
~~~

---

## Пайпы
~~~bash
cat /var/log/syslog | grep ssh | tail -n 20
# stdout одной команды → stdin другой
~~~

---

## Базовые команды файловой системы
~~~bash
pwd      # текущий каталог
cd ..    # перейти в родительский каталог
ls -lah  # содержимое каталога, включая скрытые
echo "Hello" # вывод текста
man ls   # справка
~~~

---

## QA-кейсы для практики
~~~bash
# Разделить stdout и stderr
ls /tmp /no_such_dir > out.txt 2> err.txt

# Объединить stdout+stderr
ls /tmp /no_such_dir > all.txt 2>&1

# Ошибки SSH
sudo grep "Failed" /var/log/auth.log | tail -n 50 > failed_ssh.log
~~~

---

## Мини-шпаргалка
- Терминал → shell → процессы + потоки  
- stdin/stdout/stderr — основа диагностики  
- >, 2>, &>, | — ключ к логам и пайпам  
- pwd, cd, ls, echo, man — базовые команды
