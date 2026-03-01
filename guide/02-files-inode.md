# Глава 2. Файлы, директории и inode

mkdir test-inode
cd test-inode

echo "inode data" > file1.txt
ls -li file1.txt

ln file1.txt file1_hard
ls -li

ln -s file1.txt file1_symlink
ls -li

rm file1.txt
ls -li
cat file1_hard
cat file1_symlink

lsof +L1

chmod 644 file1_hard
ls -l file1_hard

chown vlad:vlad file1_hard
ls -l file1_hard

cd ..
rm -rf test-inode

# inode проверка
ls -li /etc/passwd
stat /etc/passwd

# права
chmod 755 script.sh
chmod u+x script.sh
chmod g-w file.txt

# ссылки
ln /etc/passwd passwd_hard
ln -s /etc/passwd passwd_sym
ls -li passwd*

