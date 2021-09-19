git remote add origin https://****joinRepo***.git
# join - it is the name of repo (local)
  git fetch origin master
  git merge
  git pull origin master
  git pull origin master --rebase
  git pull = fetch + merge

.gitattributes
*.css|* text=auto|lf|crlf

.gitignore|.git/exclude
  ! исключение- после правила
  * ** dir /dir *filename

 git check-ignore -v file #test rules

INDEX
  git add --chmod=+x <file>
  git add .
  git add --force|-f <ignored file>

  git reset HEAD .idea # del from index
  echo ".idea" > .gitignore

  git add part1;commit;part2;commit
  git add -p ... - часть изменений в файле в один комит, другая часть в другой (-p)

  git add -a -m "comment"; добавляет файлы , которые уже в индексе
DELETE FROM INDEX
  git rm -r(if dir) -f(force) src = rm -r src + git add ..+ commit
  git rm -r --cached src - del from index only
  если добавил по ошибки git add src -> undo -> rm --cached

MOVE IN INDEX AND IN DIR
  git mv name1 name2
  -r dir -f rename unsaved files
  --cached - rename in index only

ALIAS
  git config --global alias.commitall '!git add .;git commit'
  Восклицательный знак используется толко, если в алиасе несколько команд
  git commitall -m '...'

COMMIT
ДЛЯ ОЗНАКОМЛЕНИЯ  =======
  Коммит - консистентное, логически завершенное изменением проекта,
  легко манипулировать - отменить / применить / передать коллеге 

  commit early, commit often
    header 50chars:component/functionality, что сделано
    описание , что бы понятно
ДЛЯ ОЗНАКОМЛЕНИЯ КОНЕЦ =======

  git commit -m 'ignore log files' .gitignore; комит без индекса, сразу в репозиторий


DELETE COMMIT
INFO =======
  Если комит достижим из ветки , на него указывает tag или reflog,
  то он остается, иначе удалются во время сборки мусора, которая 
  запускается  каждый раз при merg или fetch|pull
  git gc - call manual. Will delete unreacheable commit
  Чтобы точно удалить комит надо удалить все ссылки на него 
END INFO =======

  сборка мусора(unreacheable commits) во время merge|pull&fetch
  delete branch|tag|reflog затем вызвать git gc
  git fsck --unreachable # show  unreacheable commits
  git reflog expire --expire-now --all - ко всем reflog (удалит рефлог)
  git gc --prune=now - по умолчанию 2 недели . но --prune=now - моложе чем сейчас
  

AMEND COMMIT
  git commit --amend [--reset-author][--no-edit] = 
  git reset --soft @~ + git commit -c ORIG_HEAD
  git commit --amend -m '...' # redo last commit

COPY COMMIT
INFO =======
  Самый частый случай ее использования - если обнаружена ошибка в файле в 2х ветках
  Тогда исправляем на одной ветке деллаем коммит , а затем копируем коммит
  git cherry-pick commit-id  -  это создает новый коммит.
  отличие от слияния - слияние объединяет ветки целиком. а cherry-pick скопирует 
  указанный коммит.
  Лучше не злоупотреблять .
  Лучше создать новую ветку на коммите с ошибкой , исправить и слить в обе ветки 
  с ошибкой - сложнее , но правильнее
END INFO ======

Находясь на master 
  git cherry-pick 2702 -> new commit
                -x  - добавляет в коммент коммита инфо от куда коммит взят.
Может копировать множества коммитов
  git cherry-pick 2c11 2702 # 2 commits 
                  master..feature - все коммиты feature , 
                  которых нет в мастер.(feature minus master)
  git cherry-pick --abort  # отмена применения комитов
            --continue продолжит выполнение после разрешения конфликта
            --quit  остановиться там где мы сейчас , и сбросить 
    в отличие от merge , cherry-pick создаёт несколько коммитов. 
    Т.е. коммиты без конфликтов остаются
            --no-commit[-n] - позволяет поправить скопированный коммит, 
           перед сохранением. Добавит изменения в рабочей директории и в индексе.

Обмен комитами
  git patch 
  git apply filename
  
RESET BAD COMMIT
  HARD reset
    git reset --hard 2fad - откат к комиту 2fad
    cancel: git reset --hard ORIG_HEAD
    отмена hard reset:
    git reflog master - find commit or
    cat .git/ORIG_HEAD
    git reset --hard ORIG_HEAD

  SOFT reset
    git reset --soft @~ # переключает на предыдущий комит, но
    фс и индекс не трогает
    CANCEL: git reset --soft ORIG_HEAD

    git reset --soft @~;change script; git add script;
    git commit -c ORIG_HEAD # edit comment; -C without editing comment
    git commit -C ORIG_HEAD --reset-author

  MIXED reset (default)
    передвигает ветку, сбрасывает индекс , но не трогает рабочую директорию
    git reset @~; git branch -v; git status
    git reset HEAD [file] - сбросить файл, который не хочешь менять.

    git reset id-commit filename - сбросить файл до комита, но только в индекс,
    раб.директория не меняется, при последующем комите, сохраниться версия этого комита
    редко используется
    git checkout id-commit filename -> change index and working-dir;

  KEEP - hard reset with saving changes
    git reset --keep - старается сохранить не закомиченные изменения.

  MERGE
    git reset --merge - cancel bad merge

Отмена действий
  cat .git/logs/HEAD
  git reflog [HEAD|branch-name]
     641da64 (HEAD -> fix) HEAD@{0}: commit: some2
     cbd2546 (master) HEAD@{1}: checkout: moving from master to fix
     ...
  git branch feature HEAD@{4}
  git help reflog
  git reflog = 'git log --oneline -g'
   не достаточно, чтобы был комит , необходимо , чтобы была запись в reflog

  git reflog --no-decorate
  git checkout @{-2} - 2 checkouut назад
  git checkout - = git checkout @{-1}

  gc.reflogExpire = "90 days ago" # garbage collection
  gc.relogExpireUnreachable="30 days ago"

BRANCHES
 isolated develop flow. First commit - auto create master branch
 .git/HEAD -> branch name
 .git/refs/heads/<branch_name> -> on last commit

типы ветвления:
  тематические ветки/версии проекта/версии релиза/тематически-релизные

 create:
    git branch <name>|git checkout -b|-B <name>
 switch:
    git checkout|switch <name>
 delete:
    git branch -d fix - del reference fix from .git/refs/fix,
       удалит если ветка была влита.
    git branch -D fix - force delete. но комиты еще сохраняются,
       они просто не достижимы.
    git branch -f feature id-commit - to restore

 switch status of project to last commit of current branch:
 git checkout -f HEAD = git checkout -f = git checkout -f @~

- если не доделан fix, но надо переключиться на другую ветку, то
   git stash; git checkout branch2; git checkout branch1; git stash pop
   git stash list
  внимательно,применять stash pop надо на той ветке на которой сделано,
  но можно восстановить на др

 - если fix слишком большой и требует новой ветки,то
     git checkout -b fix -> all changes on new brach

Передвижение по веткам вручную.
Ситуация 1: сделали комиты не на той ветке.
  git branch fix - все коммиты в fix;
   а master надо вернуть на пару коммитов назад:
    git checkout fix (уйти с master);
    git branch master required_commit -f 
    -f - create branch if not exist; mv if exist
    git branch master fix - restor master.
      =
    git checkout -b master 54c4
    git checkout -B master 54c4 - mv if exist, then will switch to it

Прямое переключение на commit -
   порождает состояние отделенного HEAD.HEAD -> commit (must on branch)
   если сделать комит, то родителем становится комит на который указывал HEAD
   такие коммиты не участвуют не в одной из ветвей разработки.
   git со временем удаляет такие коммиты. чтобы исправить -
   git branch branchName id-commit
   или копируем отдельно комит
   git checkout fix; git chery-pick 9e28

 Получение более старых версий не трогая остальных.
   git checkout <[branchName|id-commit]> filename [,filenameNN]
  + добавит эти файлы в индекс
  git reset filename [,filenameNN] ## for cancel
  Если не нравится то что сделали с файлом и хотим вернуть
  состояние текущего комита:
   git checkout HEAD filename = git checkout filename
  если имя файла совпадает с именем ветки
  git checkout -- master

   git branch --merged  - показывает ветки объединенные с текущей.
   git branch --no-merged  -  не объединенные ветки
   в больших проектах практично .

GIT LOG
  Вывод истории: git log, форматирование коммитов
  By def, print commits from HEAD, can specify manual
  git log master - master branch only

  git log --pretty=short|medium(def)|full|fuller|format:''(config)|raw
  git log --oneline --no-decorate

  Диапазоны комитов:
    git log id-commit # reacheable from id-commit
         master feature --graph # reachable from master and feature
         --all --graph # reachable from all refs
         feature ^master # reach from fea.. minus master
         master..feature --boundary - include пограничный комит
         master...feature - симметрическая разность - комиты достижимые
           из master или feature, но не обоих одновременно.
         master...feature --boundary --graph

  Комиты меняющие нужные файлы
    git log vagrant.sh
    git log -p filename
            -p --follow filename - если файл был переименован
    git log feat..master vagrant.sh

  Поиск в истории , фильтры для git log
    git log --grep Run - с учетом текущей ветки
         --grep Run feat - поиск на ветке feat
    git log --grep Run -grep sayHi
    git log --grep Run --grep sayHi --all-match (logic and)
    git log --grep 'say(Hi|Bye)' -P   - perl regex
    -i  register undependent

    git log -Grestart_docker  -  -G -поиск по изменениям
    git log -Grestart_docker -p func.sh - ограничить вывод файлом

    git log -L 3,6:index.html - все коммиты в которых менялись строки с 3-6
    !!!!!!!!!

    git log -L :FuncName:script.js - для поиска можно настроить регулярное
    выражение, однако по умолчпнию git считает , что любая строка , которая н
    начинается _ $ char - это объявление функции.

    Иногда это работает.

   git log -L '/^function restart_docker/','/^}/':func.sh

    Можно искать по любым полям коммита.
    git log --author=Ilya
    git log --author=n.silonov@gmail.com
    git log --committer=silonov

    git log --before '3 months ago'
    git log --before '2017j-09-13'
    git log --after '2017-09-13'
    git log --after '2017-09-13'
    git log --after '2017-09-13 08:30:00 +02'

   git cherry master feature - выведет коммиты feature для , которых есть эквивалентные
   в master
   - означает коммит для , которого есть копия в master
   + нет копии
   git cherry master feature -v - чтобы вывести описание.
   git cherry feature    - 2я ветка по умолчанию = HEAD

   cherry - exit by historical reason

   git log feature...master  - симметрическая разность. коммиты которые есть в первой
   или во 2й ветках но не являются для них общими.
   git log --oneline --cherry-pick feature...master  - удаляет эквивадентные коммиты
   из вывода
   --cherry-mark  - помечает эквивалентные коммиты знаком =
   --left-right  > right   < left
   --left-only  - вывод только из левой ветки.

git log --oneline --cherry-mark --right-only feature...master
   = git log --oneline --cherry feature...master, который подразумевает 2 этих флага
   и флаг --no-merges который удаляет коммиты слияния.

BLAME Кто написал эту строку
    Бывает так , что участок кода не понятен или содержит ошибку.
    Интересно кто его написал для этого есть команда
    git blame
    git blame func.sh

    git blame func.sh --date=short -L 5,8
    git show commit-id
    git blame commit-id:func.sh -L 5,8  -  как угораздило написать такой код

SHOW print changes in files for every commit
  git show [HEAD|id-commit]
  1-2 steps back from current branch
  git show HEAD~ (~ current commit in HEAD) HEAD~1
  GIT SHOW head~~ (parrent) = HEAD~2
  ~~~ = HEAD~3
  @ = HEAD - сокращение исключительно для HEAD.
  git show @~ = git show HEAD = git show master
  git show master~3

Чтобы посмотреть весь файл
  git show @~:index.html - просто выведит содержимое
  git show id-commit:filename - из любого комита вывести без замены
  git checkout @~|id-commit index.html - Заменит в директории
  git show :index.html - показать файл из индекса

  git show :/saySomething - search in comment of commit

ПРОСМОТР, СРАВНЕНИЕ КОМИТОВ, ВЕТОК
  git diff master feature
           master...feature|feature...master
  git diff  - working-dir and index
  git diff HEAD - working-dir and repository
  игнор не отслеживаемых файлов

  git diff --cached|--staged - compare index and repository, what changes you whant to commit
  git commit -v # show changes to commit; git config --global commit.verbose true
  git diff [filenaem or dir] - compare working-dir and index
  git diff HEAD index.html
  git diff master feature index.html script.js

git diff --name-only master feature

  git diff -- filename   -  recomend form

  git diff commit1 commit2
  git diff commit1..commit2  - BRANCH DIFF - что сделано в ветке commit2 с момента разделения с
  веткой commit1

  git diff @
  git diff commit1:path1 commit2:path2
  git diff --no-index path1 path2 - сравнивает любые 2а файла -  не зависимо есть ли репозиторий


Tags
  еще один вид ссылок на комиты - тэги
  Как правило для пометки релизов используется
  git log - видны тэги в выводе

  git tag v1.1.0 id-commit
  на основе тэга можно создать ветку
    git branch newBranch v1.1.0
  git tag  - list tags; git tag contains 54a4
  git tag -n - tag with messages;
  git tag -n -l 'v1.1*' - filters
  
  2 types:
  1 - only link on commit
  2 - link with annotation
    git tag -a -m 'version 1.1.0' v1.1.0 54a4
    отличие от простой метки - наличие комента автора и даты
  git show v1.1.0 - who , when, diff

  git describe - описание комита на основе ближайшего тэга

MERGE
  статус был чистый, технически допустимо не закоммиченные изменения в файлах
  для каждого файла срвниваются их три версии:
   1. Которая была в их общем предке , до разделения - base
   2. Та которая на текущей ветке - ours - наша версия
   3. Та которая из features - theires - их , сливаемая
  base -> ours -> theirs

   git берет базовую версию за основу и добавляет изменения из нашей и сливаемой
   версии , и таким образом мы получаем новую версию файла с изменениями , которые
   внесены в обеих ветках. При этом может так быть , что мы поменяли файл в одном и
   том же месте по разному. git покажет сообщение, и попросит выбрать .
   Это прерванное слияние. поменяли файл в одном и том же месте по разному

   git запоминает коммит в котором мы осуществляем слияние в файле
   cat  .git/MERGE_HEAD, В НАшем случае это полный идентификатор feature

   script.js изменен в одной из веток , неважно в какой , в merge попадает более
   новая версия файла.

   если все версии файла отличаются друг от друга, то:
   git diff -U0 54a4 master index.html
   -U0 - количество обрамляющих строк в выводе diff

    git checkout --ours index.html  - вытащит файл из ветки master
    версию взять из feature
    git checkout --theirs index.html - feature
    git checkout --merge index.html

    git reset --hard  -  отменить все изменения и слияние отменить.

    git reset --merge - аккуратнее сработает , оставляет не закоммиченные изменения
    в файлах , которые не учавствовали в слияниях, т.е. которые в обеих ветках одинаковы.

  git merge feature
  git merge-base master feature - покажет общий коммит

  предположим правка ядра linux и есть много не закоммиченных изменений,
  git внесет в индекс только те файлы , которые изменены в обеих ветках
  Далее если мы хотим отменить слияние, то reset --soft отменит все изменения 
  в файлах при условии , что они внесены в индекс, включая конфликтные. Но
  не трогает те , в которых изменения только в рабочей директории.
  Таким образом изменения , которыые не касаются слияния, спокойно переживут 
  не только слияние , но и его отмену. В отличие от жесткого ресета ,
  который удаляет вообще всё.

  Для удобство у merge есть флаг --abort
 git merge --abort  =  git reset --merge

 git checkout --conflict=diff3 --merge index.html  -  добавляет еще вывод , то
  что было в базовой версии

  git config --global merge.conflictStyle diff3

        git show :1:index.html   :1 - номер стадии
        git show :2:index.html   :2 - ours  - наша, версия из мастер
        git show :3:index.html   :3 - theirs - их , из feature

        И пока в индексе конфликт, 3 версии одного файла. Многие команды git 
        работают не так как обычно или не работают вообще.

        Что бы это исправить достаточно обновить файл в индексе 
        git add index.html
        git merge --continue = git commit

Коммит слияния , дальнейшие слияния
  Коммит слияния имеет 2 родителя
  В обычном коммите записывается инфо о родителе, ид коммита на основе, которого 
  он создан, т.е. куда указывал HEAD. Но при слиянии появляется MERGE_HEAD
  указывающий на feature, инфо из него так же добавляется в коммит и файл
  удаляется.
  git log --oneline --all --graph
  git show  -  merge
  показывает комбинированный diff, что добавлено из каких файлов и только
  места в которых были конфликты.

специальный diff предназначен посмотреть как разрешились конфликты, и только.

  git show --first-parrent  - отличия от первого родителя.
  git show -m  - отличия от всех родителей по отдельности

  На практике , гораздо удобнее обычный
  git diff HEAD^1  - означает первого родителя.
  git diff HEAD^2  - означает второго родителя.

   git diff HEAD^^  - ПЕРвый родитель первого родителя.
  git diff HEAD^2^ - ДЛЯ второго.

  ^ - актуально только для merge commit
   git show @^2 - посмотрим 2го родителя HEAD

   git branch --merged  - показывает ветки объединенные с текущей.
   git branch --no-merged  -  не объединенные ветки
   в больших проектах практично .

   Если у master and feature один общий предок , то слияние пройдет просто , 
   новые изменения будут добавлены в master.
   git merge feature -m 'Improve function'

   Для описания коммита есть флаг
   git merge feature --log - то в описание коммита будут добавлены комменты 
   всех коммитов с ветки feature. Можно задать лимит , например не более 5 последних
   git merge feature --log=5 . По умолчанию не более 20.

   git log master --oneline  
   Чтобы посмотреть историю разработки , есть флаг
   git log master --oneline --first-parent. Чтобы это работало нужно сливать в правильном 
   порядке, т.е. из master вливать feature 

Отмена слияния
   git reflog -4  - 4 последних записи
   git reset --hard @{3}
    or
   git reset --hard @~
   git merge feature --log / после требуемых доработак.

Семантические конфликты и их разрешения .
  изменения конфликтуют по смыслу , а не по месту в файле.
  Правильный способ исправления конфликта
  git reset --hard @~
  git merge work --no-commit
  И мы оказываемся в состоянии прерванного слияния .
  cat .git/MERGE_HEAD
  git merge --continue  or
  git commit --no-edit  - сделает коммит слияния , т.к. есть файл MERGE_HEAD
   т.е. коммит 2го родителя.

Слияние с сохранением веток, запрет перемотки --no-ff 
  Делает полноценное слияние , даже там , где можно обойтись перемоткой.
  Проблема fast-forward, в том , что из истории совершенно не понятно 
  где заканчивается ветка master и начинаются коммиты , которые были в feature

  Затрудняет историю. И если обнаружена ошибка , то как отменить слияние?
  Если только что , то
  git reset --hard ORIG_HEAD

  git merge --no-ff --no-edit feature
  При этом все изменения остаются в ветке feature
  git show --first-parent
  git reset --hard @~

  git config merge.ff false  =  --no-ff
  or
  git config branch.master.mergeoptions '--no-ff'
  git merge feature
  git merge --ff feature

Слияние без связи с источником: merge --squash
  Коггда история не нужна.
  git merge --squash fix   - берет изменения из ветки fix и применяет к текущему
  состоянию проекта.
  git diff --cached
  git commit -m 'run some function' - это будет самый обычный коммит на ветке master
  без второго родителя и всякой связи с веткой fix.
  git branch -D fix, используется при маленьких ветках и сами по себе не представляют
  ценности. Или если история веток слишком запутанная.

  git merge --squash feature  -> conflict  .git/MERGE_HEAD не создается
  git merge --abort или --continue не работают.
  git reset --merge[--hard]
  а в место git merge --continue можно использовать
  git commit -am '...'

  состояние конфликта  - это состояние индекса когда в нем присутствуют,
  сразу 3 версии файла - базовая, и 2 сливаемые, изменения в которых конфликтуют
  между собой.

  git stash pop 
  git cherry-pop  Тоже могут дать конфликт.
  исправляем конфликт
  исправляем конфликт
  git add filename
  git commit -am '...'

Стратегии слияния
  git merge -Xours <commit>
  git merge -Xtheirs <commit>
  -renormalize - нормализует концы строк для всех файлов учавствующих в
  слиянии , если ветку прислал разработчик, у которого windows
  -ignore-all-space  (space, tab, space-tab) - игнорировать различия
  в пробелах
  -no-renames. конфликт переименований .
  git merge -Xno-renames work
  -find-renames
  git merge -Xfind-renames=80 work файлы должны быть похоже на 80%
  чтобы git понял , что это переименование , а не новый файл

  -subtree=path , замержить ветку в поддиректорию другой.
   сторонние библиотеки. Для этого надо сделать локалььную ветку , 
  например назовем ее plugin , в которую будем регулярно загружать обновления 
  из ветки другого репозитория , в котором этот плагин разрабатывается

  git merge -Xsubtree=plugin --allow-unrelated-histories plugin 
  git сделает слияние подставив общего предка - пустой коммит

  git subtree  для удобства

  git submodule - отличие от subtree в том , что в директорию помещается не ветка ,
  а репозиторий целиком. один git проект лежит в поддиректории другого.

  git submodule

  octopus - осминог , используется автоматически , когда нужно слияние 
  более 2х веток.
  git merge sayBye sayHi - получаем коммит с 3я родителями.
  не имеет особых приемуществ , даже больше недостатков, т.к. если есть
  проблемы с веткой , то нельза отменить ее одну. Основная область применения , 
  большие проекты, когда много веток с разным функционалом.
  Упрощение истории - единственный плюс в такой ситуации .

  -ours - полностью игнорировать содержимое сливаемой ветки . Изменения , которые
  не дают конфликтов добавляются.
  git merge -s ours --no-edit work  - формальное обьединение историй , 
  при полном игнорировании содержимого ветки.

Перебазирование веток: rebase
  git rebase - швейцарский нож для истории разработки git.
  С её помощью можно переписывать историю: объединять / редактировать  и даже
  менять местами коммиты.
  Базовое применение - перебазирование веток.
  git rebaase master - чтобы новое состояние master было в проекте.

  git rebase --abort  - вернуть всё как было
  git rebase --quit   - остановить и ничего не возвращать - head в неопределнном
  состоянии.
  git rebase --continue - после разрешения конфликтов
  git rebase --skip   - пропустить коммит

cat .git/ORIG_HEAD  - указывает на идентификатор , от куда была перемещена ветка feature
  git reset --hard ORIG_HEAD - вернет ее обратно .
  в более сложной ситуации 
  git reflog feature
  git show --quiet feature@{1}
  git reset --hard feature@{1}

  git rebase master  -  ветка на которую надо осуществить перебазирование. 
  перенесена будет текущая ветка.
  git rebase master feature  - feature на master
  = git checkout feature + git rebase master

Rebase проти merge: сравнение подходов
  Правило использования rebase - пока ветка у вас на пк , можете
  делать с ней , что угодно. Но если она публичная и с ней работают другие люди,
  то любые перписывания истории , включая rebase - запрещены.

  При rebase возможны поломки , т.к. коммиты применяются к новому состоянию 
  проекта. Например в мастере переименовали функцию , которую вызывают в feature
  Даже если в следующем коммите исправить проблему, мы все равно получим в истории 
  пачку битых коммитов.
  При merge такой проблемы не существует. Операция слияния безопасна.
  Она добавляет новый коммит , но не меняет , вообще не трогает предыдущие.
  После перебазирования никаких следов не остается.

  Такая проблема бывает редко.
  есть возможность запускать тесты.
  git rebase -x 'cmd' master   - cmd будет выпролнятся после каждого выполненного 
  коммита. Если завершится со статусом отличным от нуля rebase завершится .

  + rebase:
  1. Проще история разработки
  - rebase:
   1. Только для приватных веток
   2. Возможны ошибки в коммитах

тесты при rebase -x flag
  git rebase -x 'node feature.mjs' master
  error
  fix && check
  git add feature.mjs
  git commit --amend --no-edit
  git rebase --continue
  OK

Перенос части ветки, rebase --onto
  Ситуация когда новые коммиты создаем не там , где нужно.
  Создали втеку fix на feature , а надо было на master.
  git rebase --onto master(куда) feature(с какого момента) [fix - current]
  иногда проще использовать cherry-pick , а не rebase
  еще пример:
2 коммита с мастер на feature
  git rebase --onto feature @~2
  но надо потом возвращать master на 2 коммита назад, и feature перебазировать
  проще
  git checkout feature
  git cherry-pick master~2..master
  git branch -f master master~2

Перебазирование слияний rebase -p
  Представим , что в ветке , которую хотим перебазировать есть коммит слияния
  По умолчанию rebase пропускает коммиты слияния и делает историю коммитов линейной
  git rebase --preserve-merge master
  = git rebase -p master   - коммиты из сторонней ветки не скопировались.
  вместо них была создана копия коммита слияния. Все изменения , которые были в
  коммите слияния игнорируются.

  желательно избегать перебазирования веток со слияниями.
  или иметь ввиду особенности такого перебазирования.

Интерактивное перебазирование. rebase -i
  Когда мы разрабатываем , наши коммиты , не всегда оптимальны.
  Зачастую есть прыжки из стороны в сторону. Мы делаем какую то функцию 
  в одном коммите , затем переделываем ее в другом.
  Иногда сообщения слишком короткие и толком ничего не описывают.
  Бывают коммиты - исправления опечаток.
  git позволяет подредактировать историю с помощъю интерактивного перебазирования.
  Обычно это делают перед публикацией ветки или если ветка доделана и хочется ее
  влить в master.
  Другой частый случай когда мы хотим предложить исправления в проект с исходным 
  открытым кодом. Чтобы проще было принять и понять.
  git rebase -i master

  git config rebase.missingCommitsCheck warn/error

   выведется список всех коммитов с описанием команд:
   pick = use commit
   reword = use commit , but edit the commit message
   edit = use commit , but stop for amending
   squash = use commit, but meld into previous commit
   fixup = like "squash", but discard this commit's log message
   exec = run command ( the rest of the line) using shell
   drop = remove commit

   git config rebase.abbreviateCommands true - использовать первую букву команды везде.

   git rebase --edit-todo  - поправить оставшийся список команд.
   git rebase --continue

   данные действия делают и без перебазирования . Когда надо поправить историю
   git reset --hard feature@{1}
   перебазировать на саму себя 
   git rebase -i @~3
    смысл - мы перебазируем текущую ветку feature . Перемещению подлежат коммиты
   ветки feature , кроме тех , что достижимы как бы безымянной ветки с вершиной @~3
   иными словами коммиты ветки фичер от точки перебазирования.
   и так как перебазирование интерактивное, мы сможем сделать с этой веткой всё, что       
  захотим. 
   Или если хотим подредактировать историю всей ветки , то можно перебазировать на 
   коммит ответвления.
   Это будут новые коммиты.
   У rebase есть аналог перемотки. rebase начнет копировать коммиты с момента
   первого изменения.
   принудительно скопировать , даже если не было изменений - git rebase --no-ff

Коммиты - заплатки rebase autosquash.
  Исправить любой коммит на вершине ветки очень просто. Достаточно вызвать коммит --amend
  Если не на вершине -> тогда можно вызвать интерактивное перебазирование и 
  подредактировать этот коммит.
  rebase autosquash

  подготовим новый коммит , который эту опечатку исправляет.
  git commit -a --fixup=3265[commit-id]
  git commit -a --fixup=@~
  git show  -> will show diff with comment  fixup!

  later , if start rebase -i 
  git rebase -i --autosquash
  git config --global rebase.autoSquash true
  git rebase -i @~4    - найдя такой коммит , поставит заплатку сразу после него.

  вместо --fixup можно использовать --squash

  т.е. сначала 
  git commit -a --fixup=commit-id
  or
  git commit -a --squash=commit-id
  then 
  git rebase -i

авторазрешение повторных конфликтов.
   rerere = REuse REcorded REsolution
   rebase again 
   merge again  - after reset --hard
   merged rebase

обратные коммиты, revert
   git revert @ смотрит какие были изменения , в данном случае в текущем коммите,
   и дедает коммит с противоположными изменениями.

   используется в публичных ветках
   отменить можно произвольные коммиты.
   если изменения были перезаписаны или дополнены , то надо будет разрешить конфликт.
   Разрешается он обычным образом. 
   позволяет обращать диапазон коммитов
позволяет обращать диапазон коммитов
   git revert A..B
   будут созданы обратные им. Внутри устроен так же как cherry-pick.
   Разница в том что revert создает не копию коммита , а обратный коммит.

   Поддерживает почти все флаги cherry-pick.

Отмена слияния через revert.
   git revert 38e8 -m 1 (по отношению к 1му родителю).
   git diff @~   коммит отмены     
   можно сделать revert revert
   или
   cherry-pick 38e8 -m 1 (from 1st parent)
   Т.е. после отмены слияния , если хотим повторно влить ветку, то 
   отменяем отмену
   revert 38e8 -m 1  -> 0cc5
   revert 0cc5
   merge feature

   Лучше избегать отмену слияния. Иногда вынуждены делать отмену слияния.

   Если повторное слияние делается после перебазирования , то это чуть проще 
   отмена отмены не понадобится.

Повторное слияние с rebase.
   git rebase master feature
   git rebase --onto master 54a4 feature

   git config rerere.enabled true
   /opt/local/share/git/contrib/rerere-train.sh --all
   git rebase --onto master 54a4 ( commit on master )
   git checkout master
   git merge --no-ff --no-edit feature

   можно создать перебазировать влитую ветку , т.е. создать копию всех комитов но без merge
   git checkout feature 
   git rebase 54a4 --no-ff(даже если коммиты растут из одного места , все равно создать 
   их копии)

   В этом выпуске - рассмотрели как делать отмену публичных слияний git
   Таких для которых нужно делать revert.
   Обычно если ветку можно перебазировать , то ее перебазируют , а если нет , то 
   тогда через отмену отмены.

Форматы дат в git
   пример дат :
   2018-01-30 12:30:00-07:00
   30Т12:30:00-07:00
   Tue Jan 30 2018 12:30:00 GMT-0700
   30 jAN 2018 12:30:00
   2018-01-30 12:30
   2018-01-30
   2018.01.30
30.01.2018
   jAN 30,2018
   01/30/2018  - MOnth first
   1517315400 unix_timestamp

   git log --pretty='%ci | %s'

   git log --pretty='%ci | %s' --before='2018-01-02'
   Если вермя не указано то берется текущщее время =>
   --before='2018-01-02 16:00'
   Можно указать только время , тогда дата будет сегодняшняя. Временная зона текущая

   Часто используется такой формат:
   3 weeks = 3 weeks ago = 3.weeks.ago от текущего момента.
   --before='3 day[s]'
   --before=3.days
   --before=3.days.ago[blablabla]
   1 year 2 months 5 minutes 1 second
   yesterday
   midnight =~ 00:00
   nooon =~ 12:00
   tea =~ 17:00

   last friday  
   never
   now
   git config --global gc.reflogExpir=90.days
                                           never=(1 jan 1970)
   git reflog expire --expire=now --all
   обычно now - сейчас , но в git now какая то дата в будущем
   или аналог now - all - который действует только в датах экспирации 

форматы для вывода даты
   git clone https://github.com/git/git
   git log --pretty='%cd | %s'
   git log --prett='%cd | %s' --date

   default   Tue Jan 30 12:30:00 2018 +0300
   rfc  Tue, 30 Jan 2018 12:30:00 +0300
   iso     2018-01-30 12:30:00 +0300
   iso-strict      2018-01-30T12:30:00+03:00
   short   2018-01-30
   unix    1517304600
   raw     1517304600 +0300
   relative        2 months ago
   git log --pretty='%cd | %ss' --date=relative   красивый и короткий вывод

   format  '%F %T', see strftime

   git log --pretty='%cd | %s' --date=format:'%F %T %z'
   iso = %F[data] %T[time] %z[timezone]

git log --pretty='%cd | %s' --date=iso-local  - переводит даты в 
   локальную таймзону


Экспорт всего проекта
  git archive - o /tmp/file.zip HEAD 

Очистка проекта
  не отслеживаемые файлы не удаляются
  git clean - удаление не отслеживаемых файлов
            -d # files and dirs
            -x # del files which added in .gitignore
            -f # force
  git clean -dxf
  git reset --hard # cancel clean














































