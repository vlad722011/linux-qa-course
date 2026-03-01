# 🔹 Глава 2. Файлы, директории и inode — QA Cheatsheet

---

## 2.1 Файлы = inode + данные
~~~bash
ls -i file.txt
stat file.txt
~~~

---

## 2.2 Структура каталогов
~~~bash
pwd
ls -la /
cd /home  
ls -lh
cd ..
~~~

---

## 2.3 Права доступа
~~~bash
ls -l file.txt
chmod 644 file.txt
chown user:group file.txt
~~~

---

## 2.4 Hard link
~~~bash
ln file.txt hardlink.txt
ls -li file.txt hardlink.txt
~~~

---

## 2.5 Symbolic link
~~~bash
ln -s file.txt symlink.txt
ls -lF symlink.txt
readlink -f symlink.txt
~~~

---

## 2.6 Link count + lsof
~~~bash
lsof | grep deleted
find . -inum 123456
~~~

