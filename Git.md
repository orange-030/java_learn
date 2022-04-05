# Git🦄

## 参考文档

👉 [Git Reference ](https://git-scm.com/docs)

👉  [Git Book 中文](https://git-scm.com/book/zh/v2/)

👉 [Github 官方文档](https://docs.github.com/cn)



## 版本控制

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。



## 获取帮助

获取 git 的使用方法有三种方式：

```sh
git help <verb>

git <verb> --help

man git-<verb>
```



## 文件状态

暂存区（Working Directory）：PC 中的目录

版本库（Repository）：工作区有一个隐藏目录 `.git ` 是 Git 的版本库。

暂存区（Stage）：`git add` 会把文件修改添加到暂存区

<img src="http://store.secretcamp.cn/uPic/image-20210512233943415202105122339431620833983nvedTgnvedTg.png" alt="image-20210512233943415" style="zoom: 50%;" />



## .gitignore

一般总会有些文件无需纳入 git 的管理，也不希望它们总出现在未跟踪文件列表。通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。

在这种情况下，可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。

`.gitignore` 的格式规范如下：

- 所有空行或者以 `#` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反。



例子：

```sh
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```



glob 模式是指 shell 所使用的简化了的正则表达式。 

- 星号（`*`）匹配零个或多个任意字符；

- `[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 

- 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 

- 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等







 

# 分支🦄

## 提交流程

假设现在有一个工作目录，里面包含了三个将要被暂存和提交的文件。

暂存操作会为每一个文件计算校验和（SHA-1 哈希算法），然后会把当前版本的文件快照保存到 Git 仓库中 （Git 使用 blob 对象来保存它们），最终将校验和加入到暂存区域等待提交。

```sh
git add README test.rb LICENSE
git commit -m 'The initial commit of my project'
```

当使用 `git commit` 进行提交操作时，git 会先计算每一个子目录（本例中只有项目根目录）的校验和， 然后在 git 仓库中这些校验和保存为 "树对象" 。随后，git 便会创建一个 "提交对象"，它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。 如此一来，git 就可以在需要的时候重现此次保存的快照。

<img src="http://store.secretcamp.cn/uPic/image-20210513105123700202105131051231620874283uzfvwruzfvwr.png" alt="image-20210513105123700" style="zoom: 50%;" />



做一些修改后再次提交，那么这次产生的提交对象会包含一个指向上次提交对象（父对象）的指针。

<img src="http://store.secretcamp.cn/uPic/image-20210513105749091202105131057491620874669vRrxhDvRrxhD.png" alt="image-20210513105749091" style="zoom:50%;" />



因此，git 的分支，其实本质上仅仅是指向提交对象的可变指针，git 的默认分支名字是 master 。

在多次提交操作之后，其实已经有一个指向最后那个提交对象的 master 分支， master 分支会在每次提交时自动向前移动。

<img src="http://store.secretcamp.cn/uPic/image-202105131059058522021051310590616208747462jpK8l2jpK8l.png" alt="image-20210513105905852" style="zoom:50%;" />



## 分支创建

git 的分支创建实际上是创建了一个可以移动的新的指针

```sh
git branch testing
```

<img src="http://store.secretcamp.cn/uPic/image-202106261011379542021062610113816246734987MjLBP7MjLBP.png" alt="image-20210626101137954" style="zoom:50%;" />



## 特殊指针 HEAD

`HEAD` 是一个特殊指针，通过它 git 可以怎么知道当前在哪一个分支上。

`HEAD` 是指当前提交的快照，在 git 版本控制中代表当前节点的指针，同时也是一个文件

```sh
# 查看HEAD指向
cat .git/HEAD
```



- `HEAD~N` 表示回退 N 步

- `HEAD~N` 表示回退到 第 N 个父提交

所以，`HEAD~1` 和 `HEAD^1` 的作用是相同的，但是 `HEAD~2` 和 `HEAD^2` 就不同了，`HEAD~2` 就是指后退两步，而 `HEAD^2` 是指后退一步，后退的方向是第 2 个父提交



## 远程分支

三种分支类型：

- 本地分支（Local Branch）:本地的分支，进行开发工作的基础，例如本地分支 master

- 远程分支（Remote Branch）：服务器上的分支，例如服务器 origin 上的分支 master

- 远程跟踪分支（Remote-tracking Branch）：服务器上分支状态的引用，相当于一个只读仓库指针，不可以被本地直接修改。一旦进行了网络通信， git 就会为移动它们以精确反映远程仓库的状态。

  可以理解为它是一个保持对远程分支引用的本地分支，不能被程序员修改，只能通过网络通信来修改。

  远程跟踪分支以  `<remote>/<branch>` 的形式命名，例如 `origin/master ` ，它是名为 origin 的远程仓库分支 master 的本地副本。



执行 `git clone` 之后，git 会将远程仓库命名为 origin，拉取它的所有数据，创建一个指向它的 master 分支的指针，并且在本地将其命名为 origin/master ， git 也会创建一个与 origin/master 分支在指向同一个地方的本地 master 分支，这个 master 分支就是接下来工作的基础。

<img src="http://store.secretcamp.cn/uPic/image-20210513144413134202105131444131620888253NulMjENulMjE.png" alt="image-20210513144413134" style="zoom:50%;" />



如果在本地的 master 分支做了一些工作，在同一段时间内有其他人推送提交到 git.ourcompany.com 并且更新了它的 master 分支，即便这样，只要保持不与 origin 服务器连接并拉取数据，本地的 origin/master 分支也不会移动。

<img src="http://store.secretcamp.cn/uPic/image-202105131445363672021051314453616208883369leftp9leftp.png" alt="image-20210513144536367" style="zoom:50%;" />



👉 远程仓库和本地仓库同步

如果要与给定的远程仓库同步数据，运行 `git fetch origin` 命令。这个命令查找 "origin" 是哪一个服务器，从中抓取本地没有的数据，并且更新本地数据库，移动 origin/master 指针到更新之后的位置。

<img src="http://store.secretcamp.cn/uPic/image-20210513145539559202105131455391620888939TwI2fDTwI2fD.png" alt="image-20210513145539559" style="zoom:50%;" />







# 基本构建🦄

## config

### 三级配置文件

git 中有三级配置文件：

- `/etc/gitconfig`： 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 `git config` 时带上 `--system` 选项，那么它就会读写该文件中的配置变量。
- `~/.gitconfig` 或 `~/.config/git/config`：只针对当前用户，可以传递 `--global` 选项让 git 读写此文件，这会对系统上所有的仓库生效。
- `.git/config`：当前仓库的 git 目录中的 config 文件，只针对该仓库。 可以传递 `--local` 选项让 git 强制读写此文件，虽然默认情况下用的就是它。（需要进入某个 git 仓库中才能让该选项生效。）



### 配置相关

在开始使用 git 之前，应该进行合理的配置

```sh
# 配置写入 ~/.gitconfig 文件中

git config --global user.name superkuang1997
git config --global user.email 1120053798@qq.com 

# 查看所有配置以及它们所在的文件  --list 表示所有配置 --show-origin 表示显示原始输出 
git config --list --show-origin

# 配置文本编辑器，commit 时会用到
git config --global core.editor vim

# 来检查 git 的某一项配置
git config <key>
git config user.name

# 查看配置 -l, --list
git config --global -l
```



## init

如果你有一个尚未进行版本控制的项目目录，想要用 git 来控制它，那么首先需要进入该项目目录中，并使用 `git init` 来初始化一个本地仓库，该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 git 仓库中所有的必须文件。

```sh
cd /Users/user/my_project
git init
```





## add 

`git add` 可以理解为 "精确地将内容添加到下一次提交中"，它主要有三个作用：

- 令 git 开始跟踪一个文件，将其纳入版本控制之下
- 把已跟踪的文件放到暂存区
- 合并时把有冲突的文件标记为已解决状态

```sh
git add README.md
```





## rm

`git rm` 命令可以从已跟踪文件清单中移除文件

如果直接删除文件，在运行 `git status` 时会在未暂存清单中显示

```sh
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	deleted:    README.md
```

然后再运行 `git rm` 记录此次移除文件的操作，下一次提交时，该文件就不再纳入版本管理了。

```sh
git rm README.md
```



## restore

将文件从缓存区撤销，回到未被追踪的状态。



## status

### 查看工作树状态

```shell
git status 
```



### 紧凑输出模式

```sh
git status -s
git status --short  
```

输出中有两栏，左栏指明了暂存区的状态，右栏指明了工作区的状态。

暂存区 | 工作区

新添加的未跟踪文件前面有 `??` 标记，新添加到暂存区中的文件前面有 `A` 标记，修改过的文件前面有 `M` 标记。

```
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

`README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。





## commit

### 直接提交

```shell
# 简要说明这次提交的语句
git commit -m "message"
```



### 追加提交

有时候提交完了才发现漏掉了文件没有添加，或者提交信息写错了。此时可以运行带有 `--amend` 选项的提交命令来重新提交，在不增加一个新的 commit-id 的情况下将新修改的代码追加到前一次的 commit-id 。

最终只会有一个提交，第二次提交将代替第一次提交的结果。

```sh
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

追加提交之后，更新到远程分支

```sh
git push --force-with-lease origin <branch>
```



## mv

`git mv` 操作实际上是三条命令

```sh
mv README.md README
git rm README.md
git add README
```

如此分开操作，git 会意识到这是一次重命名。





## diff

```sh
git diff
```



## log

作者（Author）：指的是实际作出修改的人，

提交者（Commit）：指的是最后将此工作成果提交到仓库的人。

### 显示提交记录

`git log` 会按时间先后顺序列出所有的提交，最近的更新排在最上面。

```sh
git log

# 显示每次提交所引入的差异
git log --patch
git log -p

# 每次提交的简略统计信息
git log --stat  
```



### 格式化输出

`git log --pretty` 和 `git log --format` 可以展示输出的多种格式

`--pretty` 参数支持以下值，其中 `format` 参数可以自定义输出格式 

```sh
git log --pretty=oneline
git log --pretty=full
git log --pretty=fuller
git log --pretty=format:"%h - %an, %ar : %s"
```





### 查看提交号与分支结构

可以通过 `git log --oneline --decorate --all --graph` 查看分支

```sh
git log --oneline --decorate --all --graph

* ab5bcd9 (origin/main, origin/HEAD, main) hello
* 089660b (HEAD -> newFeature) add Person.java
* 69499e3 remove java
* d9bbbbd add java class
* ac7f72e enrich python script
* 876f3ff first commit - a nice try
* 5cd2b9c Initial commit
```









## clone

在本地创建一个远程仓库的拷贝

当执行 `git clone` 命令的时候，默认配置下远程 git 仓库中的每一个文件的每一个版本都将被拉取下来。

`git clone` 会在当前目录下创建一个对应的目录，并在这个目录下初始化一个 `.git` 文件夹，从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。

```shell
# 如果传递了 custom-name 参数，那么克隆的目录名字将会变为 custom-name
git clone <url> <custom-name>
```





## fetch

###  拉取分支

`git fetch ` 将本地仓库中的远程跟踪分支更新成远程仓库相应分支最新的状态，但不会改变本地仓库的状态，它不会更新本地分支，也不会修改磁盘上的文件，可以将 `git fetch` 的理解为单纯的下载操作。

```shell
git fetch <remote> <branch>

# 从默认远程仓库下载各个远程分支的所有的提交记录
git fetch

# 将远程仓库 origin 的 master 分支下载到本地仓库
git fetch origin master  

# 同时为源和目的地指定参数，如果本地分支不存在，git 会在本地仓库创建新的本地分支
git fetch <remote> <remoteBranch>:<localBranch>
```





### 新建本地分支

`git fetch` 时不指定任何 `remoteBranch`，仅保留冒号和 `localBranch` 部分，会在本地仓库新建分支

```shell
git fetch origin :bugFix
```



## pull

`git pull` 表示先拉取更新再合并到本地分支，相当于执行 `git fetch` 和 `git merge` 两条命令。

```sh
git pull <remote> <remoteBranch>:<localBranch>

# 如果远程分支是与当前分支合并，则冒号后面的部分可以省略
git pull <remote> <remoteBranch>
```



### 合并拉取

`git pull` 相当于执行 `git fetch ` 和 `git merge` 两个操作

```shell
git pull origin master

# 拉取远程分支 master，这样会更新远程跟踪分支 origin/master 的位置 
git fetch origin master; 
# 将 origin/master 合并到当前分支
git merge origin/master
```



### 衍合拉取

`git pull --rebase ` 相当于执行 `git fetch ` 和 `git rebase`

```sh
git pull origin foo --rebase

# 拉取 foo 分支
git fetch origin foo; 
# 将 origin/foo 衍合到当前分支
git rebase origin/foo
```





## push

将本地变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录。

只有当有拥有服务器的写入权限，并且之前没有人推送过时，这条命令才能生效。当你和其他人在同一时间克隆，他们先推送到上游然后你再推送到上游，你的推送就会被拒绝。必须先拉取他们的工作并将其合并进你的工作后才能推送。



### 推送分支

切换到本地仓库中的 `master` 分支，获取所有的提交，再到远程仓库 `origin` 中找到 `master` 分支，将远程仓库中没有的提交记录都添加上去。通过 `<branch>` 参数来告诉 git 提交记录来自于本地仓库的 `master`，要推送到远程仓库中的 `master`，它实际就是要同步的两个仓库的位置。

本地和远程仓库的名字要一致，如果本地分支存在，远程分支不存在，是推不上去的，因为这里是同时指定了本地分支和远程分支的名字。

```sh
git push <remote> <branch>

git push origin master
```



分别指定本地分支和远程分支：

```shell
# 将本地 localBranch 分支推到远端 remoteBranch 分支，如果推送的分支不存在，git 会在远程仓库创建新的分支
git push <remote> <localBranch>:<remoteBranch>

# 假设远程分支 bbb 不存在，该操作将本地分支 aaa 推送到远程，会新建一个 bbb 分支，但不会创建跟踪分支
git push origin aaa:bbb
```



### 推送时设置跟踪分支

如果在本地创建了新分支，但远程仓库没有对应分支，则推送时会自动创建一个远程跟踪分支



```sh
git checkout feature_dev
# 这里的分支名必须和本地分支相同，因为这里是 feature_dev:feature_dev 的省略表述
git push --set-upstream origin feature_dev

# 用冒号分别指定，本地和远程的分支名可以不同
git push --set-upstream origin feature_dev:feature:stable
```



### 删除远程分支

`git push` 时不指定 `localBranch`，仅保留冒号和 `remoteBranch` 部分，这相当于推送一个空分支到远程仓库，表示删除远程仓库的分支。

```sh
# 删除远程仓库 origin 的 remoteBranch 分支
git push origin :remoteBranch

# 通过 --delete 选项删除远程分支
git push --delete / -d origin remoteBranch
```





## remote

 origin 是 git 给克隆的仓库服务器起的默认名字，`-v` 选项会显示详细信息 

```sh
git clone https://github.com/schacon/ticgit
cd ticgit
git remote

>> origin

git remote -v
>> origin	https://github.com/schacon/ticgit (fetch)
>> origin	https://github.com/schacon/ticgit (push)
```





### 添加远程仓库

可以使用 `git remote add` 命令

现在可以在命令行中使用字符串 pb 来代替整个 URL

```sh
git remote add pb https://github.com/paulboone/ticgit
git remote -v

>> origin	https://github.com/schacon/simplegit-progit (fetch)
>> origin	https://github.com/schacon/simplegit-progit (push)
>> pb	https://github.com/paulboone/ticgit (fetch)
>> pb	https://github.com/paulboone/ticgit (push)
```

例如可以拉取 Paul 的仓库中有但自己仓库没有的信息

```console
git fetch pb
```





### 查看某个远程仓库

如果想要查看某一个远程仓库的更多信息，可以使用 `git remote show <remote>` 命令。

```sh
git remote show origin

* remote origin
  Fetch URL: https://github.com/schacon/simplegit-progit
  Push  URL: https://github.com/schacon/simplegit-progit
  HEAD branch: master
  Remote branch:
    master tracked
  Local branch configured for 'git pull':
    master merges with remote master
  Local ref configured for 'git push':
    master pushes to master (up to date)
```



### 远程仓库的重命名与移除

```sh
# 重命令
git remote rename pb paul
git remote
>> origin
   paul
 
# 移除
git remote remove paul
git remote
>> origin
```







### 修改远程仓库

`set-url`：change URL for a remote 

```sh
git remote set-url origin git@github.com:superkuang1997/My-Papers.git
```



```shell
# 查看关联的远程仓库的名称 远程仓库的名称一般默认为 origin
git remote
# 查看关联的远程仓库的详细信息
git remote -v
# 删除远程仓库的关联
git remote remove <name>
# 更新远程仓库的 url
git remote set-url origin <newurl>
# 删除关联的远程仓库
git remote remove <name> 
# 添加新的远程仓库关联 远程仓库的名称推荐使用默认的名称origin
git remote add <name> <url>
```



### 删除无效的远程跟踪分支

如果在远程版本库上删除了某一分支，该命令并不会删除本地的远程跟踪分支

```sh
# 删除失效的远程跟踪分支
git remote prune origin

# 查看需要清理的分支
git remote prune origin --dry-run
```



## tag

```
git tag v1     # 将标签命名为v1，并且明确指向HEAD
git tag v1 c1  # 将标签命名为v1，并且明确指向提交记录c1
```





## reset

将本地代码库的分支记录回退几个提交记录来实现撤销改动，不同于 `git checkout` ，回退之后，回退前版本的快照会消失。



### mixed

👉 回退到某个提交号，清空暂存区，原节点和 reset 节点之间的所有差异都会放到工作目录中，也就是需要重新 add

`git reset` 如果不加参数，那么默认使用 `--mixed` 参数

如果 IDEA 中开启了自动 add 文件，那么 mixed 和 soft 效果就完全一样了。

```sh
git reset HEAD^1

git reset --mixed HEAD^1 
```



### soft

👉 回退到某个提交号，原节点和 reset 节点之间的所有差异都会放到暂存区中。

```shell
# 保留工作目录，并把因为保留工作目录内容所带来的新的文件差异放进暂存区，即上一次 commit 之前的状态

git reset --soft HEAD~
```



### hard

👉  回退到某个提交号，原节点和 reset 节点之间的所有差异全部丢失。

重置 HEAD 的同时，重置暂存区和工作目录里的内容，即没有 commit 的修改会被全部擦除

```sh
git reset --hard  # 取消当前节点之后的所有改动
git reset --hard HEAD~  # 回退到上一个节点
```





## revert

回退某个提交记录来实现撤销改动

`git revert` 是回退某个 commit ，不是回退 "到" 某个 commit

适用场景： 如果想撤销之前的某一版本，但是又想保留该目标版本后面的版本，记录下这整个版本变动流程



例如：commit 了三个版本（v1、v2、v3），突然发现 v2 有 bug ，想要撤销 v2，但又不想撤销 v3 的提交，就可以用 `git revert` 命令来回退 v2，生成新的 v4，这个 v4 里会保留 v3 的内容，但撤销了之前 v2 的内容。

```shell
git revert <提交号>
git revert HEAD   # 回退当前提交，相当于reset
```





## clean

删除工作目录下未被纳入版本控制的文件

```sh
# 删除 -f 是必选参数，这是是一种保证措施
git clean -f  
# 被忽略的文件也会被删除
git clean -x  
```



## stash

能够将所有未提交的修改（工作区和暂存区）保存至堆栈中，用于后续恢复当前工作目录。

```sh
# 将所有所有未提交的修改保存至堆栈，不包括未纳入VCS的文件
git stash 

# 将所有所有未提交的修改保存至堆栈，包括未纳入VCS的文件
git stash -u

# 显示所有堆栈
git stash list

# 将堆栈应用到某个分支，不会清空堆栈
git stash apply <stash_name>

# 弹出堆栈的内容
git stash pop <stash_name>

# 弃用堆栈
git stash drop <stash_name>

# 弃用所有堆栈
git stash clear
```



# 分支构建🦄

## 跟踪分支

### 跟踪分支定义

跟踪分支是与远程分支有直接关系的本地分支，从一个远程跟踪分支检出一个本地分支会自动创建所谓的 "跟踪分支"

克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支，master 既是本地分支，也是一个跟踪分支，如果在本地新创建一个分支 testing，它仅仅是一个本地分支，没有跟踪任何远程跟踪分支。

被 "跟踪分支" 跟踪的分支叫做 "上游分支"，可以通过 `@{u}` 来引用上游分支。

```sh
# 检出到 main 的上游分支
git checkout main@{u}
```

跟踪分支是与远程分支有直接关系的本地分支，如果在一个跟踪分支上输入 `git pull` ，git 能自动地识别去哪个服务器上抓取、合并到哪个分支。

当克隆一个仓库时，它通常会自动地创建一个跟踪 `origin/master` 的 `master` 分支。

 

### 创建跟踪分支

1. 没有远程分支，也没有本地分支

   ```sh
   # 创建一个本地分支
   git checkout -b testing
   
   # 将本地分支推送至服务器，并将该本地分支设置为跟踪分支
   git push --set-upstream origin testing
   ```
   
   
   
2. 存在远程分支，本地不存在对应跟踪分支

   ```sh
   # 在本地创建远程分支的跟踪分支
   git fetch origin/master
   
   # 创建与 origin/master 同名的本地分支，并设置为跟踪分支
   git checkout --track origin/master
   git checkout -t origin/master
   
   # 创建与 origin/master 对应的本地分支，并设置为跟踪分支，可以其定义本地分支名
   git checkout -b custom_name origin/master
   
   # 将当前分支设置为 origin/master 的跟踪分支
   git branch -u origin/master
   git branch --set-upstream-to origin/master
   ```



3. 特殊情况

   如果尝试检出的本地分支不存在且刚好只有一个名字与之匹配的远程跟踪分支，那么 git 就会创建自动创建跟踪分支

   ```sh
   # 从服务器上将分支 main 拉取的本地，生成一个名为 origin/main 的远程跟踪分支
   git fetch main
   
   # 本地没有 main 分支，但本地上有 origin/main 分支
   git checkout main
   
   >>  Branch 'main' set up to track remote branch serverfix from origin.
       Switched to a new branch 'main'
   ```

   



### 删除远程分支

如果已经通过远程分支做完所有的工作了，可以运行带有 `--delete` 选项的 `git push` 命令来删除一个远程分支。 

```sh
git push origin --delete serverfix
```



##  branch

### 创建新分支

git branch 可以用于创建新分支，事实上，git 只是创建了一个可以移动的新的指针

git 有一个名为 HEAD 的特殊指针，HEAD 指向当前所在的分支，总是指向当前分支上最近一次提交记录。

```sh
# 创建分支
git branch testing
```

<img src="http://store.secretcamp.cn/uPic/image-20210513110332883202105131103331620875013Dwtv9qDwtv9q.png" alt="image-20210513110332883" style="zoom: 33%;" />



```sh
# 更简洁的方式 创建分支同时切换到分支
git checkout -b <branch-name>

# 是以下两条命令的简写
git branch <branch-name>
git checkout <branch-name>
```





### 查看跟踪分支

`git branch -vv` 可以查看设置的所有跟踪分支

`origin/iss53: ahead 2`：本地有两个提交还没有推送到服务器上

`teamone/server-fix-good: ahead 3, behind 1`：服务器上有一次提交还没有合并入同时本地有三次提交还没有推送

```sh
git branch -vv

>>    iss53     7e424c3 [origin/iss53: ahead 2] forgot the brackets
      master    1ae2a45 [origin/master] deploying index fix
    * serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] this should do it
      testing   5ea463a trying something new
```





### 查看本地和远程分支

如果不加任何参数运行 `git branch` ，会得到当前所有分支的一个列表：

```sh
git branch

>>    iss53
    * master
      testing

# 查看所有远程分支  remote
git branch -r  

# 查看所有本地和远程分支  all
git branch -a

# 

# 查看每一个分支的最后一次提交
git branch -v

>>    iss53   93b412c fix javascript issue
    * master  7a98805 Merge branch 'iss53'
      testing 782fd34 add scott to the author list in the readmes
      
# 查看哪些分支已经合并到当前分支（master）      
git branch --merged

>>    iss53
    * master

# 查看所有包含未合并工作的分支
git branch --no-merged

>>  testing
```



###  删除分支

```sh
# 如果一个分支已经被合并到主分支，那么它通常可以被删除
git branch -d <branch-name>

# 如果一个分支还没有被合并，用以上命令删除则会被拒绝，可以用 -D 强制删除
git branch -D <branch-name>

# 删除远程分支，删除后还需推送到服务器
git branch -d -r <branch> 
git push origin <branch>

# 删除远程分支，推送即删
git push -d origin <branch>
```



### 重命名分支

```sh
# 重命名本地分支
git branch -m <oldbranch> <newbranch>
# 强行重命名本地分支，j
'shi
git branch -m <oldbranch> <newbranch>
```





### 重命名远程分支

假设本地分支

```sh
# 先重命名本地分支
git branch -m <oldbranch> <newbranch>
# 删除远程分支
git push --delete origin <oldbranch>
# 将本地分支推送到远程仓库，并将本地分支设置为其跟踪分支
git push --set-upstream origin <newbranch>
```





## checkout

Git 引入了两个新命令 `git switch` 和 `git restore`，用以替代现在的 `git checkout` ，因为这个命令承担了太多职责，既被用来切换分支，又被用来恢复工作区文件，对用户造成了很大的认知负担。

`git checkout` 的核心功能包括两个方面，一个是分支的管理，一个是文件的恢复。这两个核心功能，未来将由 `git switch` 和 `git restore` 分别负责。



### 切换分支

`git checkout` 的作用是移动 `HEAD` 的位置，可以切换到任意提交点，使用了 `git checkout` 后 `HEAD` 则会指向所在位置。

```sh
git checkout testing
```

<img src="http://store.secretcamp.cn/uPic/image-20210513110922495202105131109221620875362lPtyCJlPtyCJ.png" alt="image-20210513110922495" style="zoom: 33%;" />



如果再提交一次，会有以下变化：

testing 分支随着提交操作自动向前移动，master 分支没有变化。

<img src="http://store.secretcamp.cn/uPic/image-20210513111047659202105131110471620875447jJphcJjJphcJ.png" alt="image-20210513111047659" style="zoom:45%;" />



也可以将分支切换回 master

```sh
git checkout master
```

这条命令做了两件事：

- 使 HEAD 指回 master 分支
- 将工作目录恢复成 master 分支所指向的快照内容



<img src="http://store.secretcamp.cn/uPic/image-20210513111244380202105131112441620875564ea5ztXea5ztX.png" alt="image-20210513111244380" style="zoom:38%;" />



### 创建分支并切换

```sh
git checkout -b newBranch
```





## switch

### 切换分支

```sh
git switch topic  # 切换分支 
```



### 创建分支

```sh
git switch -c topic  # 切换分支并创建
```



## merge

### 简单分支合并

Fast-forward 表示快进，如果想要合并的分支是当前分支的直接后继，git 会直接将指针向前移动。

即如果顺着一个分支走下去能够到达另一个分支，那么 git 在合并两者的时候，只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧。

```shell
# 合并某个分支到当前分支
git merge <branch-name>

>>  Updating ca82a6d..4b25a1f
    Fast-forward
     aaaa | 1 +
     1 file changed, 1 insertion(+)
     create mode 100644 aaaa
```



### 三方分支合并

对于更复杂的情况，咋需要做三方合并

<img src="http://store.secretcamp.cn/uPic/image-20210513114531417202105131145311620877531kwRKx9kwRKx9.png" alt="image-20210513114531417" style="zoom: 50%;" />

假设在分支 iss53 完成了 bug 修复，现在需要将分支合并回 master

此时，master 所在提交不是 issue53 的直接祖先，git 会使用两个分支的末端所指的快照（C4、C5）以及这两个分支的公共祖先（C2），做一个简单的三方合并。

```sh
git checkout master
git merge issue 53

>>  Merge made by the 'recursive' strategy.
    index.html |    1 +
    1 file changed, 1 insertion(+)
```

<img src="http://store.secretcamp.cn/uPic/image-20210513114608133202105131146081620877568uaiU2XuaiU2X.png" alt="image-20210513114608133" style="zoom:51%;" />



git 将此次三方合并的结果做了一个新的快照并且自动创建一个新的提交指向它，这个被称作一次合并提交，它的特别之处在于他有不止一个父提交。

<img src="http://store.secretcamp.cn/uPic/image-20210513114922704202105131149221620877762W4EmrvW4Emrv.png" alt="image-20210513114922704" style="zoom: 50%;" />



### 分支合并冲突

如果合并的分支之间都涉及到修改了同一处，那么在合并时就会冲突

此时需要手动解决冲突

```sh
git merge issue57

>>  CONFLICT (add/add): Merge conflict in README.md
    Auto-merging README.md
    Automatic merge failed; fix conflicts and then commit the result.
    
    
    
git status
>>>   You have unmerged paths.
      (fix conflicts and run "git commit")
      (use "git merge --abort" to abort the merge)

    Unmerged paths:
      (use "git add <file>..." to mark resolution)
      both added:      README.md
```



浏览冲突文件，可与看到 `======` 区分了两个不同的分支版本，在手动解除冲突后，使用 `git add` 来将其标记为冲突已解决。

```
<<<<<<< HEAD
origin master context
=======
iss57 fix context
>>>>>>> issue57
```







## rebase

可以提取在 C4 中引入的补丁和修改，然后在 `C3` 的基础上应用一次。 在 git 中，这种操作就叫做衍合（rebase）。 

可以使用 `rebase` 命令将提交到某一分支上的所有修改都移至另一分支上，可以创造更线性的提交历史。

<img src="http://store.secretcamp.cn/uPic/image-20210513162135749202105131621361620894096J7HMAeJ7HMAe.png" alt="image-20210513162135749" style="zoom:45%;" />



可以检出 `experiment` 分支，然后将它衍合到 `master` 分支上

```sh
git checkout experiment

# 将experiment衍合到master
git rebase master
```

它的原理是首先找到这两个分支（即当前分支 `experiment`、衍合操作的目标基底分支 `master`） 的最近共同祖先 `C2`，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件，然后将当前分支指向目标基底 `C3`， 最后以此将之前另存为临时文件的修改依序应用。

<img src="http://store.secretcamp.cn/uPic/image-20210513162233029202105131622331620894153ku17NIku17NI.png" alt="image-20210513162233029" style="zoom: 50%;" />



最后回到 `master` 分支，进行一次快进合并。

```sh
git checkout master
git merge experiment
```

<img src="http://store.secretcamp.cn/uPic/image-20210513163001809202105131630021620894602qLP1s7qLP1s7.png" alt="image-20210513163001809" style="zoom:50%;" />



```shell
# commit-2 衍合到commit-1 ，如果没有 commit-2，则默认值为 HEAD
git rebase <commit-1> <commit-2>  

# 将 head 衍合到 commit-1
git rebase <commit-1>

# 将 bugFix 衍合到 master
git rebase master bugFix     
```



### 简单衍合

假设有两个分支 master 和 topic，将 topic 衍合到 master

```sh
# 在master分支
git rebase master topic

# 或者在topic分支
git rebase master
```



```
         A---B---C topic
        /
    D---E---F---G master     

          ↓↓↓

                 A'--B'--C' topic
                /
    D---E---F---G master
```



加入分支中有相同的提交，在衍合中会自动跳过该提交，以下 A 与 A' 相同

```
           A---B---C topic
          /
     D---E---A'---F master
     
        			↓↓↓

                   B'---C' topic
                   /
     D---E---A'---F master
```



### 交互式衍合

```sh
# 交互式衍合从当前 head 到 15f745b，不包括 15f745b
git rebase -i 15f745b

# 交互式衍合从 start-commit-id 到 endpoint-commit-id
git rebase -i [start-commit-id] [endpoint-commit-id]
```



### 衍合冲突

```sh
git rebase --continue  # 解决冲突后输入，继续衍合
git rebase --skip      # 绕开冲突的commit
git rebase --abort     # 删除.git/rebase-apply，直接结束衍合
```



## describe

描述离当前位置最近的锚点，得到 `<tag>_<numCommits>_g<hash> `的格式输出

```shell
git describe  <commit-id>
git describe  # 描述最近锚点到HEAD
```





## cherry-pick

```shell
# 将指定的提交号复制到当前位置HEAD的下方
git cherry-pick <commit-id>
```



# 其他命令🦄

## ls-remote





# Options🦄

## Generic options

```
Generic options
    -v, --verbose         show hash and subject, give twice (-vv)  for upstream branch
    -q, --quiet           suppress informational messages
    -t, --track           set up tracking mode (see git-pull(1))
    -u, --set-upstream-to <upstream>
                          change the upstream info
    --unset-upstream      Unset the upstream info
    --color[=<when>]      use colored output
    -r, --remotes         act on remote-tracking branches
    --contains <commit>   print only branches that contain the commit
    --no-contains <commit>
                          print only branches that don't contain the commit
    --abbrev[=<n>]        use <n> digits to display SHA-1s

```





## -C

在给定的 path 下运行，而不是在当前目录运行

```sh
git -C <path> <COMMAND>
```

以下表示在 `$(brew --repo)` 的目录下运行 git

```sh
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git
```





# IDEA Git🦄

## 分支的颜色

黄色：HEAD

绿色：本地分支

紫色：远程分支

白色：tag



# 其他问题🦄

## github搜索

关键词：

- 找百科大全 awesome XXX 

- 找例子 xxx sample

- 找空项目架子 xxx starter/ xxx boilerpla 

- 找教程 xxx tutorial



高级搜索：

```sh
# star
datastructure stars:>1000

# 编程语言
language:java datastructure stars:>1000

# 用户
user:superkuangzero
```





## host修改

`git clone` 特别慢是因为 github.global.ssl.fastly.net 域名被限制了。

查询该域名的 IP 地址，并添加到 hosts 即可。

查询网址：https://github.com.ipaddress.com/

```
140.82.113.3     github.com
199.232.69.194   github.global.ssl.fastly.net
```



## gitee曲线救国

当 clone 的是自己的项目时速度很慢又没有别的好方法时：

```shell
1. 将 github 的 repo 导入 gitee

2. 从 gitee 克隆项目
git clone https://gitee.com/superkuang1997/BlogByDjango

3. 将关联仓库重新设置回github
git remote set-url https://github.com/superkuang1997/BlogByDjango
```





## 设置代理

需要配合代理服务器使用

```sh
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy http://127.0.0.1:7890
```





## 中文乱码

`git status` 时中文字符无法正确显示

```sh
git config --global core.quotepath false
```



## 使用SSH

参考官方文档：https://docs.github.com/cn/github/authenticating-to-github/connecting-to-github-with-ssh



## 管理多个SSH Key

修改~/.ssh/config文件，增加以下文件内容



