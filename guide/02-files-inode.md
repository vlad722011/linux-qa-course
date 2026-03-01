Guide 02: Файлы и inode — почему "удалил файл, а место не освободилось"
Цель главы
После этой главы ты должен уметь:
Разобрать ls -l по частям (права, inode, link count)
Создать/проверить hard link vs sym link
Найти "призрачные" файлы через lsof +L1
Починить права доступа для QA-скриптов

1) Файл в Linux = inode + данные
Имя файла = ссылка на inode в каталоге
inode содержит: тип, права, владелец, link count, даты, блоки данных

Практика (на VPS)
ls -li /etc/passwd
stat /etc/passwd
ls -l /etc/passwd

2) Разбор ls -l (тренировка чтения)
-rw-r--r-- 2 vlad vlad 1024 Mar 1 file.txt
││  │  │ │ │    │  │   └─дата+имя
││  │  │ │ │    │  └────размер
││  │  │ │ └─────группа
││  │  │ └──────владелец
││  │  └────────link count
││  └───────────права остальных
│└─────────────права группы
└──────────────права user + тип

Практика
touch ~/test-rights.txt
ls -l ~/test-rights.txt
chmod 644 ~/test-rights.txt
ls -l ~/test-rights.txt
rm ~/test-rights.txt

3) Hard link vs Sym link
Hard link = ещё одно ИМЯ для ТОГО ЖЕ inode
Sym link = отдельный inode с путём к файлу

Практика (обязательно!)
mkdir ~/inode-lab
cd ~/inode-lab
echo "QA данные" > original.txt
ln original.txt hard.txt
ln -s original.txt sym.txt
ls -li
rm original.txt
ls -li
cat hard.txt
cat sym.txt
cd ..
rm -rf ~/inode-lab

4) "Диск полный, файлов нет" — классика QA
rm file.txt НЕ удаляет inode полностью!
inode живёт, если: другие hard link ИЛИ процесс держит открытым

Диагностика
df -h
du -sh /*
lsof +L1
lsof | grep deleted

5) QA-кейс: освободить диск на CI
mkdir ~/disk-check
cd ~/disk-check
echo "== Диагностика диска ==" > disk-report.txt
echo "df -h:" >> disk-report.txt
df -h >> disk-report.txt 2>/dev/null
echo "Призраки:" >> disk-report.txt  
lsof +L1 | head -10 >> disk-report.txt
ls -lah disk-report.txt
cat disk-report.txt

6) Задания (с проверкой)
Создай original.txt
ln original.txt hard.txt → ls -li (одинаковый inode)
ln -s original.txt sym.txt → ls -li (разный inode)  
rm original.txt
cat hard.txt (работает!)
cat sym.txt (ошибка!)

7) Мини-инцидент (как на работе)
Симптом: "CI: No space left on device", df -h = 100%
Выполни:
df -h /
du -sh /var/log/* | sort -hr | head -3
lsof +L1 | head -5 > ghosts.log
cat ghosts.log

Гипотезы:
1. Логгер держит удалённый /var/log/app.log
2. CI-раннеры держат temp файлы
Решение: kill PID из lsof
