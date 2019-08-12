# Git 使用
## 安装Git
```bash
sudo apt-get install git
```
设置用户名和邮箱
```
git config --global user.name "yourname"
git config --global user.email "your e-mail address"
```
## 创建版本库
第一步、首先建立一个空目录作为本地仓库。

第二步、通过 `git init` 命令将该目录变为Git可以管理的仓库。

## 向本地仓库中添加文件
例如向仓库中加入readme.md文件。

第一步、将文件拷贝到本地仓库的目录下。使用 `git add filename` 将文件添加至本地仓库中。

第二步、使用命令 `git commit` 将文件提交到仓库中。
```git
git commit -m "added readme.md"
```

## 版本回退
`git log` 命令可以查看历史版本更改信息。需要回退版本时可以使用 `git reset` 命令。
```
git reset --hard HEAD^
```
HEAD在git中表示的是当前版本，上一个版本就是HEAd^，上上个版本就是HEAD^^。更多向上的版本可以用 `~num` 来代替，例如向上100个版本可以写作HEAD~100。

如果版本回退错误，此时如果终端还没有关闭或者有其他的记录包含有之前的版本id，可以直接用保存下来的id信息再重新reset回去。或者还可以使用 `git reflog` 命令，查看命令历史来确定要回退到未来的哪一个版本。

## 工作区和暂存区
工作区：能在本地里看到的目录。比如创建的本地仓库文件夹就是一个工作区。

版本库：工作区有一个隐藏目录 `.git` ，这个不算工作区，而是Git的版本库。

版本库中包含暂存区(stage/index)，Git自动为我们创建的第一支分支master，以及指向master的一个指针叫HEAD。

提交一个文件进入Git版本库，是分为2步进行的：

- 使用 `git add` 将文件添加到暂存区；
- 使用 `git commit` 将暂存区的所有内容提交到当前分支。

## 管理修改

Git中跟踪管理的是修改，并不是文件。

其中`git add`负责将修改放入到暂存区，`git commit`负责将暂存区的修改提交到当前分支。

正常的工作流程：

```shell
第一次修改 -> git add -> 第二次修改 -> git add -> git commit
```

`git commit`提交后可以使用`git diff Head -- file`查看工作区与版本库中最新版本的区别。

## 撤销修改

如果修改现在只停留在工作区，还没有使用`git add`提交到暂存区中，可以使用`git checkout -- file`丢弃工作区的修改。

如果已经使用`add`将修改提交到暂存区中了，可以使用两种方法：

1. `git checkout -- file`撤销修改，使文件回到添加到暂存区之前的状态；
2. `git reset HEAD <file>`可以讲暂存区的修改撤销掉，重新放回工作区；

## 删除文件

在工作区中删除了一个文件，这也是一个修改操作。

在工作区删除了文件之后，可以有两种操作：

1. 确实要从版本库删除文件，使用命令`git rm file`，然后使用`git commit`；
2. 删除操作出错，使用`git checkout -- file`，这里的`git checkout`其实就是用版本库里的版本替换工作区的版本，所以不管是添加错误，还是删除错误，都可以通过该命令进行还原操作。

## 远程库操作

现在github上建立一个仓库，然后`git clone`即可

## 分支管理

### 创建与合并分支

Git中`HEAD`不是严格地指向提交的，而是指向`master`，而`master`才是指向提交，`HEAD`指向的就是当前分支。

创建分支时，例如`dev`，相当于Git新建了一个指针`dev`，指向与master相同的提交，然后将`HEAD`指向`dev`，这样就能够表示当前分支处于`dev`上。

此时，对工作区的修改与提交就是针对`dev`分支的了，此时进行一次新的提交时，实际上就是`dev`指针向前移动一步，但`master`指针保持不变。

当在`dev`分支上的任务完成后，与master的合并，实际上就是将`master`指针指向`dev`的当前提交，就完成了合并工作。

当分支合并工作完成之后，可以通过删除`dev`指针，删除`dev`分支。

#### 创建分支

`git checkout -b name`，命令中`-b`表示创建并切换，这里等同于两条命令：

```shell
git branch dev
git checkout dev
```

查看当前拥有的分支,`git branch`，显示出来的分支名称中带有`*`号的表示是当前分支。

#### 切换分支

`git checkout branch_name`切换到指定分支

#### 合并分支

`git merge branch_name`，其中`branch_name`就待合并的制定分支，使用后就是将`branch_name`指一条分支合并到了head

#### 删除分支

`git branch -d dev`删除`dev`分支，

