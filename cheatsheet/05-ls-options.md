# Cheatsheet 05: ls Options — QA

Цель: понимать разметку файлов (l@ t*), типы (d/l/-), диагностировать symlink проблемы.

**Базовые флаги:** **ls -l** (long format), **ls -a** (**скрытые**), **ls -h** (human-readable), **ls -F** (classify: /**=dir @=symlink *=exec), **ls -p** (slash только для dir).

**Типы файлов:** **d**ir, **l**ink, **-**file, **c**har device, **b**lock device.

**Symlink ls -lF:** `lrwxrwxrwx 1 dev dev 1 Mar 1 15:19 l -> t*` — цель классифицируется (`t*`), имя — нет (`l`).

**Диагностика:** **ls -lF l**, **ls -lF l | od -c**, **readlink -f l**.

**Практика:** `mkdir test && cd test && ln -s ../cheatsheet/04-history-aliases.md l && ls -lF`
