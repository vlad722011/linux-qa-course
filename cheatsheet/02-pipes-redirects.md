# Cheatsheet 02: Pipes + Redirection — цепочки для QA

Цель: собирать команды “прочитал → отфильтровал → посчитал → сохранил”, чтобы быстро разбирать логи и вывод тестов.

---

## 1) Пайпы (|)
~~~bash
cmd1 | cmd2 | cmd3
~~~

Пример:
~~~bash
grep -i "ssh" /var/log/auth.log | tail -n 20
~~~

---

## 2) Редиректы (костяк)
~~~bash
cmd > out.txt
cmd >> out.txt
cmd 2> err.txt
cmd > all.txt 2>&1
cmd &> all.txt
~~~

---

## 3) Полезные “кирпичики”
~~~bash
head -n 20 file
tail -n 50 file
tail -f file
less file
grep -i "pattern" file
wc -l
sort
uniq -c

---

## 4) QA-паттерны (копипаст)
Последние ошибки по SSH:
~~~bash
sudo grep "Failed" /var/log/auth.log | tail -n 50
~~~

Посчитать количество “Failed”:
~~~bash
sudo grep "Failed" /var/log/auth.log | wc -l
~~~

Топ IP по “Failed password” (быстрый отчёт):
~~~bash
sudo grep "Failed password" /var/log/auth.log \
  | awk '{print $(NF-3)}' \
  | sort | uniq -c | sort -nr | head
~~~

Сохранить отчёт в файл:
~~~bash
sudo grep "Failed password" /var/log/auth.log | tail -n 200 > failed_ssh_last200.log
~~~

Сохранить stdout и stderr отдельно:
~~~bash
some_command > out.log 2> err.log
~~~

Сохранить всё в один файл:
~~~bash
some_command > all.log 2>&1
~~~

---

## 5) Мини-задачи (самопроверка)
1) Сделай файл `failed_ssh_last50.log` с последними 50 строками “Failed” из auth.log.
2) Сделай файл `ssh_top_ip.txt` с топ-10 IP (команда выше).
3) Сделай “артефакт”: запусти `ls /tmp /no_such_dir` так, чтобы всё попало в `all.log`.
