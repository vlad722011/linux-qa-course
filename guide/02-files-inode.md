# Guide 02: Файлы и inode — реальный разбор (QA)

---

## 1) Что такое inode простыми словами
**inode** — это "паспорт" файла на диске. Содержит метаданные: размер, права, timestamps, **но НЕ ИМЯ**.

Один inode = один набор метаданных. Несколько имён (hard links) могут указывать на один inode.

---

## 2) Показать inode командой
~~~bash
**ls -i** file.txt      # inode номер файла
**ls -i** /etc/passwd  # системный файл
**stat** file.txt      # все метаданные (включая inode)
~~~

---

## 3) Демо: создай hard link и увидишь
~~~bash
echo "test" > file1.txt
ln file1.txt file2.txt      # hard link (один inode)

ls -li file1.txt file2.txt  # одинаковые i-node номера!
stat file1.txt file2.txt    # одинаковые метаданные
~~~

**Важно:** `rm file1.txt` не удалит данные — `file2.txt` держит inode живым.

---

## 4) Soft link (symlink) vs hard link
### 4.1 Hard link (ln)
~~~bash
ln orig.txt copy.txt    # тот же inode
ls -li orig.txt copy.txt  # одинаковые номера
~~~

### 4.2 Soft link (ln -s)
~~~bash
ln -s orig.txt link.txt  # **отдельный** inode, указывает на имя
ls -li orig.txt link.txt # **разные** inode!
ls -lF link.txt          # l@ -> orig.txt
~~~

**rm orig.txt** → **hard link** живёт, **symlink** ломается (dangling).

---

## 5) Диагностика: inode помогает найти дубли
~~~bash
# Найти все файлы с inode 123456
find /path -inum 123456

# Два имени → один файл (экономия места)
ls -li /var/log/syslog /var/log/syslog.1  # часто одинаковые inode
~~~

---

## 6) Практика: найди утечки места (QA-кейс)
Сценарий: `df -h` показывает место, `du -sh *` — меньше.

**Причина:** удалённый файл держится открытым процессом (inode живёт).

~~~bash
echo "big" > bigfile && sleep 3600 < bigfile &  # держим открытым
rm bigfile                                      # файл удалён с ls
lsof | grep deleted                             # inode живёт!
kill %1                                         # освободим место
~~~

