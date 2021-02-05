---
categorys:
  - 代码管理
tags:	
  - Git
---

代码的变动可以使用**merge**将分支合并到其他分支，或者可以只辅助分支的一部分提交到其他分支，对应的命令是**cherry-pick**，但是这种办法并非万无一失，有可能增加分支的复杂度，也可能导致一些难以追溯的错误。

## 基本用法

```
git cherry-pick <commitHash>
```

```bash
# 切换到 master 分支
$ git checkout master

# Cherry pick 操作
$ git cherry-pick f
```

```bash
a - b - c - d   Master
     \
       e - f - g    Feature

# cherry-pick f
a - b - c - d - f   Master
     \
       e - f - g    Feature
```

## 转移多个提交

```bash
# 多个提交
git cherry-pick <HashA> <HashB>

# 连续提交
git cherry-pick A..B 

# 连续提交，不包括A
git cherry-pick A^..B 
```

## 配置项

### -e，--edit

打开外部编辑器，编辑提交信息。

### -n，--no-commit

更新工作区和暂存区，不产生新的提交。

### -x

提交信息的末尾追加一行(cherry picked from commit ...)

### -s，--signoff

提交信息的末尾追加一行操作者的签名，表示是谁进行了这个操作。

### -m parent-number，--mainline parent-number

如果要转移的节点是一个合并节点，Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动。-m配置项告诉 Git，应该采用哪个分支的变动。它的参数parent-number是一个从1开始的整数，代表原始提交的父分支编号。

```bash
$ git cherry-pick -m 1 <commitHash>
```

其他详细参考--help

## 代码冲突

操作过程中发生代码冲突，Cherry pick 会停下来，让用户决定如何继续操作。

### --continue

解决代码冲突后，将修改的文件重新加入暂存区

```bash
$ git cherry-pick --continue
```

### --abort

代码冲突后，放弃合并，回到操作前的样子。

### --quit

代码冲突后，退出 Cherry pick，但是不回到操作前的样子。

## 隐患

如果在**cherry-pick**之后，继续操作分支，修改相同文件，最后合并时会因为feature分支自身造成冲突

```bash
a - b - c - d   Master
     \
       e - f - g Feature

# cherry-pick f
a - b - c - d - f   Master
     \
       e - f - g Feature
       
# 合并Feature到Master
a - b - c - d - f	- g'(conflict)	Master
     \						/
       e - f - g 				Feature
```

在2分支的情况下，可以追溯代码，但是如果是多分支中，因为部分合并无法追溯到真正进行**cherry-pick**的位置，可能导致无法追溯，埋下隐患，最终bug。

------

**参考**

[阮一峰-git cherry-pick 教程](http://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html)

[Raymond Chen-Stop cherry-picking, start merging, Part 1: The merge conflict](https://devblogs.microsoft.com/oldnewthing/20180312-00/?p=98215)