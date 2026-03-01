Guide 02: Файлы и inode — почему "удалил файл, а диск полный"
Цель главы
По окончании ты поймёшь:
Почему файл в Linux = inode (метаданные) + данные на диске
Разницу hard link vs sym link на практике
Как диагностировать "диск полный, файлов нет" — типичная QA-задача
Как работают права доступа rwx и chmod/chown

1) Файл в Linux — это НЕ то, что ты думаешь
Имя файла (test.txt) = просто ярлык в каталоге
inode = настоящий файл с метаданными:
• тип (- файл, d папка, l symlink)
• права доступа rwx
• владелец/группа  
• размер, даты, link count
• указатели на блоки данных

Практика (выполни на VPS)
ls -li /etc/passwd
stat /etc/passwd
# inode номер одинаковый у hard link!

2) Права доступа — как это работает
-rw-r--r-- 2 vlad vlad 1024 Mar 1 file.txt
1символ: тип файла
234: rwx пользователя
567: rwx группы  
8910: rwx остальных
11: link count (2!)
12-21: владелец:группа

Практика
touch ~/rights-test.txt
ls -l ~/rights-test.txt
chmod 644 ~/rights-test.txt    # rw-r--r--
ls -l ~/rights-test.txt
chmod u+x ~/rights-test.txt    # +execute для себя
ls -l ~/rights-test.txt
rm ~/rights-test.txt

3) Hard link vs Symbolic link — главная разница
Hard link = ещё одно ИМЯ для ТОГО ЖЕ inode
Sym link = отдельный inode со строчкой "путь=../realfile"

Практика (обязательно выполни)
mkdir ~/links-lab && cd ~/links-lab
echo "секретные QA-данные" > original.txt
ln original.txt hard_link.txt      # тот же inode!
ln -s original.txt sym_link.txt    # отдельный файл-путь

ls -li                          # смотри inode!
rm original.txt
ls -li                          # hard живёт, sym мёртв!
cat hard_link.txt               # работает!
cat sym_link.txt                # ошибка!

cd ~ && rm -rf ~/links-lab

4) Почему "диск полный, файлов нет" — классика QA
rm file.txt НЕ удаляет inode полностью!
inode живёт пока:
a) есть другие имена (hard link)
b) процесс держит файл открытым

Диагностика
df -h                 # 100% заполнен
du -sh /*             # места много!
lsof +L1              # ← ЗОЛОТАЯ КОМАНДА! показывает удалённые+открытые
lsof | grep deleted

5) QA-кейс: срочно освободить диск CI-сервера
Сценарий: тесты не запускаются "No space left"
mkdir ~/disk-rescue
cd ~/disk-rescue
( echo "== df =="; df -h;
  echo "== ghosts =="; lsof +L1 | head -10;
  echo "== biglogs =="; du -sh /var/log/* 2>/dev/null | sort -hr | head -5 ) > disk-report.log

cat disk-report.log

6) Задания (выполни + проверь)
1. Создай файл data.txt → ls -li покажет inode номер
2. ln data.txt hard.txt → ls -li покажет ОДИНАКОВЫЙ inode  
3. ln -s data.txt sym.txt → ls -li покажет РАЗНЫЙ inode
4. rm data.txt → cat hard.txt (работает), cat sym.txt (ошибка)
5. Покажи "призраков": lsof +L1 | wc -l | cat ghost-count.txt

7) Мини-инцидент (реальный кейс с работы)
Симптом: "CI не сохраняет артефакты, df -h 100%, du -sh /* = 20GB"
Диагностика (выполни):
df -h /
du -sh /var/log/* | sort -hr | head -3
lsof +L1 | grep log | head -5

Гипотезы:
1. Логгер-демон держит удалённый /var/log/app.log
2. CI-раннеры держат временные файлы
Решение: kill PID из lsof → диск освободится!

Команды главы (шпаргалка)
ls -li file           # inode + права
stat file             # все метаданные  
ln file link          # hard link
ln -s file link       # sym link
lsof +L1              # призраки диска ← TOP для QA!
chmod 755 file        # rwxr-xr-x
chown user:group file # смена владельца
