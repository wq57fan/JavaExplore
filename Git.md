# Git基本指令

## 一、常用步骤

### 1、提交作业

#### 1.1 远程仓库关联

把一个已有的本地仓库与远程Github仓库关联：

```shell
$ git remote add origin git@github.com:USERNAME/FILENAME.git
```

#### 1.2 添加至暂存区

本地代码修改完后，首先将当前目录下修改的所有代码从工作区添加到暂存区：

```shell
$ git add .
```

其次“注释”将缓存区内容添加到本地仓库：

```shell
$ git commit -m "COMMENTS"
```

#### 1.3 远程仓库推送

把本地库的所有内容推送到远程库上：

```shell
$ git push -u origin master
```

#### 1.4 登陆失败？

若登陆失败，无法push则执行：

```shell
$ git config --system --unset credential.helper
```

执行这个命令之后，你可以重新写入账号密码，这样就可以重新提交代码了。

### 2、Github仓库改名后本地同步

#### 2.1 列出远程库名

本地仓库路径下执行以下代码，列出每个远程库的简短名字：

```shell
$ git remote -v
```

#### 2.2 删除远程仓库

在本地仓库删除远程仓库：

```shell
$ git remote rm origin
```

#### 2.3 修改名称

修改Github仓库名称和本地路径名称，同时本地文件夹的名称也改一下。

#### 2.4 添加远程仓库

在本地添加新的远程仓库，在本地的仓库路径下键入：

```shell
$ git remote add origin git@github.com:USERNAME/FILENAME.git
```

## 二、常用指令

### 1、创建一个仓库

```shell
$ mkdir learngit	#创建一个空目录
$ cd learngit
$ pwd				#用于显示当前目录
$ git init			#把这个目录变成Git可以管理的仓库
```

### 2、把文件添加到版本库

第一步，用命令git add告诉Git，把文件添加到仓库：

```shell
$ git add readme.txt
```

第二步，用命令git commit告诉Git，把文件提交到仓库：

```shell
$ git commit -m "wrote a readme file"
```

### 3、版本回退
```shell
$ git status				# 展示仓库当前的状态
$ git diff readme.txt		# 查看difference 	
$ git log					# 显示从最近到最远的提交日志
$ git log --pretty=oneline  # 如果嫌输出信息太多，每次提交信息都用单行输出
$ git reset --hard HEAD^	# 回退到上一个版本（HEAD指向的版本就是当前版本）
$ git reset --hard 3628164  # 回到3628164的版本
$ git reflog				# 看命令历史，以便确定要回到未来的哪个版本
```

### 4、撤销修改
场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file

```shell
$ git checkout -- readme.txt	
# 把readme.txt文件在工作区的修改全部撤销，让这个文件回到最近一次git commit或git add时的状态
```

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD file，就回到了场景1，第二步按场景1操作。

```shell
$ git reset HEAD readme.txt		
# 可以把暂存区的修改撤销掉（unstage），重新放回工作区（用于已add但没commit前，git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区）。
# 若要继续删记住再用$ git checkout -- readme.txt丢弃工作区的修改
```

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退

### 5、删除文件
```shell
$ git rm test.txt					# 在本地把test.txt文件删了
$ git commit -m "remove test.txt"	# 直接提交修改
```

### 6、把本地仓库的内容推送到GitHub仓库
```shell
$ git remote add origin https://github.com/wq57fan/123.git	# 在本地关联远程库，远程库的名字是origin（Git默认的）
$ git push -u origin master	  # 把本地库的所有内容推送到远程库上
$ git push origin master	  # 从现在起，只要本地作了提交，就用它把本地master分支的最新修改推送至GitHub
```

### 7、创建与合并分支
```shell
# 创建分支：git branch <name>
# 切换分支：git checkout <name>

#上面两句可以合并：
$ git checkout -b dev	# 创建dev分支，然后切换到dev分支
$ git branch			# 列出所有分支，并查看当前分支
```

提交作业后切换回主分支再合并：

```shell
$ git checkout master		# 切换回master分支
$ git merge dev				# 把dev分支的工作成果合并到master分支上
$ git branch -d dev			# 删除dev分支
```

### 8、发生冲突
当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
用git log --graph命令可以看到分支合并图：

```shell
$ git log --graph --pretty=oneline --abbrev-commit		# 看分支的合并情况
```

## 三、总结

| 代码                                                        | 内容                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| git init                                                    | 初始化一个空的git本地仓库                                    |
| git status                                                  | 查看工作区代码相对于暂存区的差别                             |
| git diff 文件名                                             | 就是查看文件不同                                             |
| git clone URL                                               | 就是克隆远程仓库到本地                                       |
| git pull                                                    | 将远程仓库代码拉取到本地并合并（当前分支自动与唯一一个追踪分支进行合并） |
| git log                                                     | 显示从最近到最远的提交日志                                   |
| git reflog                                                  | 看命令历史，以便确定要回到未来的哪个版本                     |
| git reset --hard HEAD^                                      | 回退到上一个版本（HEAD指向的版本就是当前版本）               |
| git rm test.txt                                             | 在本地把test.txt文件删了                                     |
| git remote -v                                               | 查看远程库的信息                                             |
| git remote add origin https://github.com/wq57fan/库名字.git | 在本地关联远程库，远程库的名字是origin（Git默认的）          |

