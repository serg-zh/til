# Использование нескольких репозиториев с одним проектом

```shell
$  https_proxy=proxy_server git clone https://github.com/serg-zh/til.git
$ git remote add local git@gitlab-srv.nkbvs.tsure.ru:serg/til.git
$ git remote
local
origin
$ git push -u local --all
Counting objects: 34, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (31/31), done.
Writing objects: 100% (34/34), 19.20 KiB | 6.40 MiB/s, done.
Total 34 (delta 2), reused 0 (delta 0)
To gitlab-srv.nkbvs.tsure.ru:serg/til.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'local'.
serg@serg:~/work/til$ git push -u local --tags
Everything up-to-date
```

[ссылка на stackoverflow](https://stackoverflow.com/questions/14290113/git-pushing-code-to-two-remotes)

Now, if you want to push to two or more repositories using a single command, you may create a new remote named all (as suggested by @Adam Nelson in comments), or keep using the origin, though the latter name is less descriptive for this purpose. If you still want to use origin, skip the following step, and use origin instead of all in all other steps.

So let's add a new remote called all that we'll reference later when pushing to multiple repositories:

```shell
$ git remote add all git://original/repo.git
$ git remote -v
all git://original/repo.git (fetch)               <-- ADDED
all git://original/repo.git (push)                <-- ADDED
origin  git://original/repo.git (fetch)
origin  git://original/repo.git (push)
$ git config -l | grep '^remote\.all'
remote.all.url=git://original/repo.git            <-- ADDED
remote.all.fetch=+refs/heads/*:refs/remotes/all/* <-- ADDED
```

Then let's add a pushurl to the all remote, pointing to another repository:

```shell
$ git remote set-url --add --push all git://another/repo.git
$ git remote -v
all git://original/repo.git (fetch)
all git://another/repo.git (push)                 <-- CHANGED
origin  git://original/repo.git (fetch)
origin  git://original/repo.git (push)
$ git config -l | grep '^remote\.all'
remote.all.url=git://original/repo.git
remote.all.fetch=+refs/heads/*:refs/remotes/all/*
remote.all.pushurl=git://another/repo.git         <-- ADDED
```

Here git remote -v shows the new pushurl for push, so if you do git push all master, it will push the master branch to git://another/repo.git only. This shows how pushurl overrides the default url (remote.all.url).

Now let's add another pushurl pointing to the original repository:

```shell
$ git remote set-url --add --push all git://original/repo.git
$ git remote -v
all git://original/repo.git (fetch)
all git://another/repo.git (push)
all git://original/repo.git (push)                <-- ADDED
origin  git://original/repo.git (fetch)
origin  git://original/repo.git (push)
$ git config -l | grep '^remote\.all'
remote.all.url=git://original/repo.git
remote.all.fetch=+refs/heads/*:refs/remotes/all/*
remote.all.pushurl=git://another/repo.git
remote.all.pushurl=git://original/repo.git        <-- ADDED
```

You see both pushurls we added are kept. Now a single git push all master will push the master branch to both git://another/repo.git and git://original/repo.git.