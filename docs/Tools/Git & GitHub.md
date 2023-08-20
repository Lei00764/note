# Git & GitHub

## 什么是 Git？

> 分布式版本控制系统
> - 分布式：不需要联网，在自己的机器上就可以使用
> - 版本控制：记录、管理、回溯文件的修改历史

Git 的三个区：工作区、暂存区和版本库

### 配置 Git

#### 命令：`git init`

创建一个本地的 Git 版本库（使用当前目录作为 Git 仓库）

### Git 基础用法

#### 命令：`git add .`

将文件添加到暂存区

> `.` 表示全部文件
> 当然，也可以将指定的文件加到暂存区 git add file_name

特点：只会添加修改过的文件

#### 命令：`git status`

查看当前工作区和暂存区的状态

三种状态：
> 未跟踪 Untracked
> 已跟踪 Tracked
> 忽略 Ignored

#### 命令：`git commit -m "message"`

提交更改

- 不使用 -m 选项：打开编辑器，输入提交信息，-m 适合简短的信息，方便

#### 命令：`git log`

查看所有提交历史

常用选项：
> --oneline：以每一个提交占一行的形式显示
> --graph：显示分支结构
> --stat：显示文件修改信息
> -p：显示详细的修改内容
   注：一个短划线表示简写，两个短划线表示全称

#### 有关"branch"的命令

```bash
git branch branch_name # 基于当前 HEAD 指向创建一个分支

git branch # 查看分支
git show-branch # 更详细

git checkout branch_name # 切换分支
git checkout -b branch_name # 先创建一个分支，再切换到该分支

git diff branch_name1 branch_name2 # 比较两个分支
git diff branch_name # 比较工作区和xx分支
git diff # 比较工作区和暂存区
```

#### 有关"merge"的命令

```bash
git merge branch_name1 branch_name2 # 将多个分支的更改都合并到当前分支
```

##### 三种 merge 情况

> already up-to-date：当前分支 比 被合并分支 多提交 --- nothing changes
> fast-forward：被合并分支 比 当前分支 多提交 --- HEAD 将指向被合并分支
> merge commit：当前分支和被合并分支都有新的提交，且修改了同一处内容，即遇到冲突，需要手动解决冲突

merge 操作一般都在 GitHub 上通过 pull request 完成，此时，自己之前的提交信息其实是没必要的，log 信息很乱，反而会有干扰，因此我们可以使用squash merge 或 rebase 在提交前处理自己之前的各种 log 信息。
```bash
git merge --squash  branch_name# 将相较于 branch_name 分支多出来的所有 commit 信息合并成一个commit，再 merge 到目标分支
```

如果有多个程序员同时开发一个项目，那么在代码历史树上将会有多个 branch 和 主 branch 交织在一起，而 rebase 不会出现多个分支交织的显示，rebase 永远都是一条线。

#### 命令：`git reset xxxx`

读档，回到之前某一提交的状态

常用选项：
> --soft：只修改 HEAD 指针
> --mixed：默认选项，只修改 HEAD 指针和暂存区
> --hard：全部修改，完全回退

#### 关于 commit message

意义：记录更改的原因/内容，方便定位/回溯

Angular 规范： https://github.com/angular/angular/blob/main/CONTRIBUTING.md#-commit-message-format
```markdown
<type>(<scope>): <short summary>
  │       │             │
  │       │             └─⫸ Summary in present tense. Not capitalized. No period at the end.
  │       │
  │       └─⫸ Commit Scope: animations|bazel|benchpress|common|compiler|compiler-cli|core|
  │                          elements|forms|http|language-service|localize|platform-browser|
  │                          platform-browser-dynamic|platform-server|router|service-worker|
  │                          upgrade|zone.js|packaging|changelog|docs-infra|migrations|
  │                          devtools
  │
  └─⫸ Commit Type: build|ci|docs|feat|fix|perf|refactor|test
```
> type：更改类型
> scope：影响范围
> summary：更改的简要描述
> body：详细描述
> footer：标签

```markdown
BREAKING CHANGE: <breaking change summary>
<BLANK LINE>
<breaking change description + migration instructions>
<BLANK LINE>
<BLANK LINE>
Fixes #<issue number>
```

#### 删除文件的两种情况

- 删除暂存区文件，但保留工作区的文件：`git rm --cached`

如果想把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 --cached 选项即可：

- 同时删除本地和版本库的文件：`git rm` 
> 若要修改远程仓库，即 GitHub 中的文件，还需要`git commit -m "delete xxx"

#### 什么是 HEAD？

HEAD 指向的分支就是当前分支

#### .gitignore

规定忽略哪些文件
语法：
- `#`：开头表示注释
- `*`：匹配一个或多个字符，`**`：匹配中间目录
- `/`：放在开头表示根目录

可以去网上找找常用语言的 .gitignore 模版

## 注意点

- 合并分支时的注意点：假设要把 C 分支的代码合并到 A 分支，则必须先切换到 A 分支上，再运行 git merge 命令，来合并 C 分支！如果两个人修改的是一个文件的不同位置，在使用merge指令时，git 会自动帮我们合并，但若修改的是同一位置，此时会出现冲突！需要人为修改。
- 删除分支时的注意点：删除分支不能在该分支上删除该分支
- 不在分支没有被 pr(pull request) 之前 merge，最好少 commit，不然最后 merge 时非常困难，可以用rebase -i命令在本地压缩 commit 到一个里面
- 在使用 `git reset` 的 hard 模式之前, 你需要再三确认选择的存档是不是你的真正目标. 如果你读入了一个较早的存档, 那么比这个存档新的所有记录都将被删除! 这意为着你不能随便回到"将来"了。

前面介绍的是自己一个人管理 github 仓库的流程，下面介绍多人协作的方法。

## Github 工作流

```bash
git clone https://github.com/ultralytics/yolov5.git  # 克隆文件到本地

git checkout -b my_feature  # 创建并切换到新分支 效果：复制一份当前分支的内容到新分支上（现在有两个分支，其内容是一样的）

git diff  # 查看自己对代码的修改内容(相较于现在远程仓库现有内容)

git add 文件  # 把文件放到暂存区 让git知道我有一些代码想提交

git commit -m "提交信息"  # 将暂存取的内容提交到本地git，也就是让git管理

#以上是完成了本地管理

git push origin my_feature  # 将本地my_feature分支的内容push到github上 效果：github上多了一个my_feature分支

# 如果当push代码时发现，github仓库上main分支更新了，那么我就要测试在更新后，我更新的代码是否正确，因此，我现在需要将github更新后的内容同步到我的my_feature分支。
git checkout main  # 切换到本地的main分支，磁盘内容会发生修改

git pull origin main  # 把github上的现有分支main的内容（也就是上面说的main分支更新后的内容）拉取到本地

git checkout my_feature  # 此时磁盘内容有我自己修改的，但没有github上更新的

# rebase 自己的文件
git rebase main  # 同步这两个代码 效果：把我的修改先放在一边，然后把github上的修改拿过来，然后在此基础之上，再把我的修改加上去
# 在这个过程中，有可能会出现rebase conflict，就需要手动选择需要哪一个代码

git push -f origin my_feature  # -f force

# 上述步骤，我们将修改后的代码成功push到github上的my_feature分支，接着就需要等仓库的主人接受我们的分支，即我们提交pull request，请求仓库主任把我这个分支给pull到这个项目

# 仓库主人一般使用squash merge
# 效果：把这一个分支上的所有改变合并成一个改变，然后commit到main分支
# 然后再把远端的my_feature删掉，即delete branch

# 在我们本地，也应该把my_feature删掉
git checkout main

git branch -D my_feature # 删除my-feature分支

git pull origin master  # 再把远程的拉下来
```