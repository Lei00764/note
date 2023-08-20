# 版本控制 Git

> 如何使用 git ？
>
> 不知道，只需记住并执行这些 shell 命令就可以进行协同工作。如果你出现错误，保存文件，再删除副本，重新下载就好。

由 C 语言实现

## git 底层设计思想

- 使用有向无环图来记录
- 使用分支来进行并行工作

folder is called as "tree".

file is called as "blob".

![截屏2023-05-28 17.08.54](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2017.08.54.png)

每一个圈对应一个快照，带有提交人信息，如author、message等

快照 snapshow 对应 git 中的术语 commit

```python
type blob = array<byte>
type tree = map<string, tree | blob>
type commit = {
	parents  : array<commit>
	author   : string
	message  : string
	snapshot : tree
}
type object = blob | tree | commit
objects = map<string, object>
哈希函数：将一对数据转成一个长字符串
```

``` python
def store(o):
	id = sha1(o) # SHA-1 哈希
	objects[id] = 0
	
def load(id)
	return objects[id]
```

Git 维护一组对象，和一组引用

```
references = map<string, string>
用人可以理解的东西去对应哈希编码，而不是直接用随机的哈希编码
```

## git 命令

对对象或引用的操作

```shell
git init # 仓库可视化

ls -a

ls .git # objects 中存对象、refs 中存引用

echo "hello world" > hello.txt

git status 

# git 给用户更多选择，可以自由选择想要上传的
git add hello.txt
# 添加到暂存区，相当于告诉 git，这部分内容应该包含在下一次快照中

git commit # 创建快照，在图中生成一个节点
```

> Q：哈希值到底是什么？
>
> A：git cat-file -p 42fb7a2
>
> 查看对象的具体内容

> Q：为什么要有 git add 和 git commit，而不是直接提交整个快照？
>
> A：因为有的时候我们并不希望在当前快照中保存所有内容
>
> git add 添加到暂存区，帮助我们实现这个功能

```bash
git log # 查看提交历史，默认线性排序
git log --all --graph --decorate
```

git 提供引用，可读的命名

HEAD 指向当前正在查看的提交

```bash
# 在历史记录之前移动
git checkout 42fb7a2  # 移动了 HEAD 指针
```

![截屏2023-05-28 17.41.08](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2017.41.08.png)

```bash 
git diff # 显示当前目录比最近一次commit的区别
git diff 42fb7a2 hello.txt # 执行比较的版本和比较的文件
```

HEAD 指向的是当前正在查看的提交，注意和当前的文件目录的区别，在没有提交之前，当前的文件目录可以随意修改，而当前正在查看的提交是不会变的。

注意 git 命令和底层数据模型之间的关联

```bash
git branch
git branch cat # 创建一个新分支
git checkout cat # 切换到新分支 HEAD 指向 cat
```

![截屏2023-05-28 17.54.15](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2017.54.15.png)

在开发 cat 时同时开发 dog

```bash
git checkout master
git checkout -b dog # 创建和切换
```

![截屏2023-05-28 17.56.06](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2017.56.06.png)

将两个功能合并到一个版本

先合并 cat 分支

```bash
git checkout master # 确保在 master 分支
git merge cat # 合并 cat 分支
# 一个比较有趣的事，当一个分支是以 master 分支为基础开发时，要切换到该分支，只需将 HEAD 移向该分支，而不需要创建新的快照 fast forward
```

![截屏2023-05-28 17.59.48](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2017.59.48.png)

再合并 dog 分支

```shell
git merge dog  # 想要合并 dog 分支
# CONFLICT 合并冲突
```

```shell
git mergetool  # git 提供的解决合并冲突的工具
```

![截屏2023-05-28 18.03.30](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2018.03.30.png)

删除冲突标记，修改代码

![截屏2023-05-28 18.04.01](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2018.04.01.png)

```shell
git merge --continue # 告诉 git 我们解决了冲突，继续合并
```

![截屏2023-05-28 18.04.47](https://lei-1306809548.cos.ap-shanghai.myqcloud.com/Obsidian/%E6%88%AA%E5%B1%8F2023-05-28%2018.04.47.png)

```shell
git remote  # 列出当前仓库的所有远程仓库
git remote add <name> <url> # 默认名字是 origin
```

```shell
git push <remote> <local branch>:<remote branch>
# 简化命令
git branch --set-upstream-to=origin/master # 只需要第一次关联一下远程仓库
git push
```

默认所有 git 命令都是不联网的，所以需要特殊的命令来与远程仓库通信

```shell
git feach # 与远程仓库通信，检索在远程仓库上的更改，并在本地上获取这些更改
git merge # 将本地分支更新到与远程分支指向相同的位置
```

```shell
git pull = git feach + git merge
```

```shell
git clone <url> <folder name> # 获取远程仓库的副本
```