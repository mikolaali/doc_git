REMOTE:
git remote add origin https://****joinRepo***.git
git pull urigin master --rebase

ADD:
  git add . --force|-f <ignored file>
  git add part1;commit;part2;commit
  git add -p ... - часть изменений в файле 
  git add -a -m "comment"; добавляет файлы , которые уже в индексе

  git rm -r(if dir) --cached src - del from index
  git reset HEAD .idea # del from index

  git mv -r(if dir) --cached name1 name2 ; переименовать в индексе
  если по ошибке добавил

  git config --global alias.commitall '!git add .;git commit'

  header 50chars:component/functionality, что сделано
  описание , что бы понятно

  commit
  git commit -m 'ignore log files' .gitignore; комит без сразу в репозиторий
  git commit --amend -m '...' # redo last commit
  = git reset --soft @~   +    git commit -c ORIG_HEAD

COPY:
  git cherry-pick 2c11 2702 # 2 commits copy on current branch
  --abort #cancel; --continue # after resolved conflicts
  --quit # оставить текущее сост и сбросить остальное копирование
  --no-commit[-n] - поправить коммит, перед сохранением.

DELETE:
  delete branch|tag|reflog затем вызвать git gc
  git branch -D branchName
  git tag -d tagName
  git push --delete origin tagName 
  git reflog expire --expire-unreachable=now 
  
  git fsck --unreachable # show  unreacheable commits
  git show id-commit; or ; git checkout id-commit # to look what is the state
  git gc
  
  git prune -n|--dry-run #report what would be removed ;delete blob from repo

HARD RESET (ветка на указаныый комит, директория и индекс тоже)
  git reset --hard 2fad - откат к комиту 2fad всей директории. отдельно файл нельзя.
  git reset --hard ORIG_HEAD(.git/ORIG_HEAD) # откат предыдущего reset'a

MIXED RESET (DEFAULT) (ветка на указанный комит, индекс сбрасывается, директория остается)
  git reset HEAD|id-commit [file] - в индексе, в состояние HEAD|id-commit
  директория не меняется
  git checkout if-commit file -> change dir and index

SOFT RESET (BRANCH refer to previous commit, dir and index not changed)
  git reset --soft @~ 
  CANCEL: git reset --soft ORIG_HEAD
  
MERGE RESET 
  git reset --merge - cancel bad merge

CANCEL ACTIONS  
  less .git/logs/HEAD  # reflog file
  git reflog [HEAD|branch-name]  =  git log -g --oneline [HEAD|branchName]
  git checkout -b|-B branchName HEAD@{4} # -B switch to branchName

BRANCHES
 isolated develop flow. First commit - auto create master branch
 .git/HEAD -> branch name
 .git/refs/heads/<branch_name> -> on last commit
  branchType: тематические ветки/версии проекта/версии релиза/тематически-релизные

 create: 
   git branch <name>  |  git checkout -b|-B <name>
 switch: 
   git checkout|switch <name>
 delete: 
   git branch -d|-D(force) fix # delete .git/refs/fix,
 setSpecialCommitForBranch:      
   git branch -f feature id-commit 
 resetCurrentChanges:   
   git checkout -f [HEAD|@~]
 saveChangesOnNewBranch:   
   git checkout -b fix -> all changes on new brach
 moveMaster2CommitsBack:
   git checkout fix (уйти с master);
   git branch master required_commit -f
      -f - create branch if not exist; mv if exist 
   equal
   git checkout -B master 54c4 # afte switch to it
               -b  #don't switch
 showMergedBranch:  
   git branch --merged|--no-merged

SAVE CHANGES
 git stash; git stash pop; git stash list  #save you work in cache

ОТДЕЛЕННЫЙ HEAD: git branch branchName id-commit # to save
   or   git chery-pick id-commit

GET FILE(S) FROM ANY COMMIT TO WORKING-DIR and INDEX:
  git checkout <[branchName|id-commit]> filename [,filenameNN]
  git reset filename # cancel
  
GIT LOG:
  git log --pretty=short|medium(def)|full|fuller|format:''(config)|raw --graph --oneline
  master featur|feature ^master|master..feature|master...feature  --boundary
  git log [fix..branch] fileName [-p(show diff)] #commits that change file
  SEARCH IN HISTORY:
    git log --grep Run [--grep patt]|-P(regex) -i(case insensitive)
!    git log -Grestart_docker -p func.sh #search restart_docker in func.sh in commits 
!    git log -L 3,6:index.html - коммиты в которых менялись строки с 3-6
    git log -L :FuncName:script.js # отслеживание изменений в функции
    --author=name|email;  --before '3 months ago'; --after '2017-09-13 08:30:00 +02'
  
BLAME Кто написал эту строку:
  git blame [commit-id:]func.sh -L 5,8 

SHOW print changes:
  git show [HEAD|id-commit]
  git show id-commit:filename - из любого комита показать файл целиком
  git show :index.html - показать файл из индекса
 
COMPARISON COMMIT:
  git diff master feature|master...feature|feature...master|fix..master [file|dir]
  --name-only|--no-index(люые файлы, даже вне репозитория)
  git diff #working dir vs index 
  git diff HEAD # dir vs repo
  git diff --cached # index vs repo
  git diff commit1:path1 commit2:path2

TAG:
  git tag v1.1.0 id-commit
  git tag # list tags
  git show v1.1.0

MERGE:
  .git/MERGE_HEAD(ID of new commit);
  git checkout master; 
  git merge [--log=5|--no-ff(no fast forward)] fix #  fix in master
  -renormalize|-ignore-all-space|-subtree=path|--allow-unrelated-histories
  git submodule
  git checkout --ours|--theirs|--merge(base) index.html # version to use;
  git reset --hard  # cancel merge
  git merge-base master feature - покажет общий коммит
  git merge --abort = git reset --merge
  git show :1|:2|:3:index.php # :1 - base, :2 - ours, :3 - theirs
  git show -m  - отличия от всех родителей по отдельности
  git diff HEAD^1(1st parent)|HEAD^2(sec parent)|HEAD^^(1st par of 1st par)
  
  git branch --merged|--no-merged
  
  git merge --squash fix # if history not needed. no MERGE_HEAD
  git merge work --no-commit  # в прерванном слиянии, если семантический конфликт
  git add .; git merge --continue (or) git commit  # to end merge

  git reset --hard @~ # CANCEL merge
  git reset --hard ORIG_HEAD

REBASE (for editing dev history):
  rebase - пока ветка у вас на пк, если публичная, то исправление истории запрещено.
  .git/ORIG_HEAD # от куда была перемещена ветка
  git rebase master # current branch on master
  git rebase master feature # feature on master
  git rebase --abort|--quit|--continue|--skip|--preserve-merge
  git reset --hard ORIG_HEAD
  
  git rebase --onto master(куда) feature(с какого момента) fix|current
  git rebase --onto feature @~2 [HEAD]
  =git checkout feature; git cherry-pick master~2..master; git branch -f master master~2
  
  git rebase -i master # интерактивное перебазирование
  git rebase --edit-todo|--continue

  git rebase -i @~3|boundary-commit  # redo last 3 commits| redo all branch.
    
REVERT (используется в публичных ветках.для любых комитов.диапазон комитов, почти
  все флаги cherry-pick.)
  git revert id-commit

FORMATS
  3 weeks = 3 weeks ago = 3.weeks.ago 
  git log --date=default|rfc|iso| !!!short|unix|relative

EXPORT PROJECT
  git archive - o /tmp/file.zip HEAD

CLEAN PROJECT
  не отслеживаемые файлы не удаляются
  git clean - удаление не отслеживаемых файлов
            -d # files and dirs
            -x # del files which added in .gitignore
            -f # force
  git clean -dxf
  git reset --hard # cancel clean
