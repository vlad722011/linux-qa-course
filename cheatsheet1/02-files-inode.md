# Cheatsheet 02: Files + ls + inode — QA

Цель: понимать файлы, типы (d/l/-), inode, диагностика symlink.

---

## 1) ls Options (базовые)
~~~bash
**ls -l** (long) **-a** (скрытые) **-h** (human) **-F** (classify /** @ *\*) **-i** (inode)
~~~

**Типы:** **d**ir **l**ink **-**file **c**har **b**lock

---

## 2) Symlink диагностика
~~~bash
ls -lF link.txt          # l -> target*
ls -lF link.txt | od -c  # проверить символы
readlink -f link.txt     # реальный путь
~~~

**Практика:** `ln -s file.txt link && ls -lFi`

---

## 3) inode (паспорт файла)
~~~bash
ls -i file.txt
stat file.txt
find . -inum NNN        # все файлы с inode NNN
~~~

**Hard link:** `ln file1 file2` → **один inode**
