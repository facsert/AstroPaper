---
author: facsert
pubDatetime: 2022-07-08 10:17:53
title: Git Local Repository
postSlug: ""
featured: false
draft: false
tags:
  - git
description: "Git 本地仓库"
---

<!--
 * @Author       : facsert
 * @Date         : 2022-07-08 10:17:53
 * @LastEditTime : 2022-12-15 23:00:54
 * @Description  : edit description
-->

## 介绍

本地仓库是

## 创建

使用 `git init` 创建本地仓库, 在创建位置会创建 .git 隐藏文件夹
仓库所有配置及版本文件均保存在 .git 文件夹中

```bash
 $ mkdir -p ~/Desktop/repository                 # 在左面创建 repository 文件夹
 $ cd ~/Desktop/repository                       # 进入 repository 文件夹

 $ git init                                      # 在 repository 目录下创建本地仓库
 > Initialized empty Git repository in /root/Desktop/repository/.git/

 $ ls -a                                         # 查看创建的隐藏文件夹
 > .  ..  .git
```

## 状态

> git-status - Show the working tree status

git 用一下3种描述表示**文件状态**, 进一步可划分为**未追踪**和**已追踪**
工作区 -- `git add` --> 暂存区 -- `git commit` --> 本地仓库

| 区域                            |      描述      |              状态              |
| :------------------------------ | :------------: | :----------------------------: |
| `Untracked files`               |    新增文件    |    文件处于工作区, 未被追踪    |
| `Changes not staged for commit` | 有新修改的文件 | 文件处于工作区, 新修改未被追踪 |
| `Changes to be committed`       |  无修改的文件  |   文件处于暂存区, 修改已追踪   |

```bash
 $ git status                                    # 查看本地仓库状态, 有新增文件未被记录
 > Untracked files:                              # 新增文件 first_file.txt
 >      first_file.txt
 >
 > Changes to be committed:                      # ADD 新文件 first_file.txt, 待 commit
 >      new file:   first_file.txt
 >
 > Changes not staged for commit:                # 已被记录文件 first_file.txt，又有新修改但未被记录
 >      modified:   first_file.txt
```

> git-add - Add file contents to the index

追踪本地仓库的修改, 追踪的文件内容进入暂存区, 等待新的 ADD 覆盖或者 commit 生成版本

```bash
 $ git add <file>                                # 记录指定文件修改
 $ git add .                                     # 记录当前目录下所有文件修改(上层文件未记录)
 $ git add --all                                 # 记录仓库目录下所有文件修改(推荐)

 $ git status                                    # 新增文件已被记录
 > Changes not staged for commit:                # 文件被修改但未被记录
         modified:   first_file.txt

 $ git add --all                                 # 记录 repository 目录下所有修改

 $ git status
 > Changes to be committed:                      # 文件修改已被记录, 待 commit
         modified:   first_file.txt
```

> git-commit - Record changes to the repository

将暂存区的文件内容生成可回溯的版本

```bash
 $ git commit -m "<commit message>"              # message 较短描述可直接填写
 $ git commit -s                                 # message 较长, 使用默认编辑器编辑 commit
 $ git commit --amend                            # 在上次 commit 基础上修改, 并替换原来的 commit
 $ git commit --amend --no-edit                  # 使用上次 commit 且不修改, 即本次 commit 和上次合并

 $ git checkout --<file>                         # 撤销工作区的修改, 回到上次 commit 状态

 $ git status
 > Changes to be committed:                      # 文件修改已被记录, 待 commit
         modified:   first_file.txt

 $ git commit -m "first commit"                  # 暂存区文件内容生成版本
 >  1 file changed, 1 insertion(+)

 $ git status                                    # 修改已保存生成版本
 > nothing to commit, working tree clean

 $ git log                                       # 查看 commit 版本信息
 > commit 38c1df5cf2bed00f5b7365ee4913916e25238009 (HEAD -> master)
 > Author: facser <root@facser>
 > Date:   Mon Feb 13 20:42:18 2023 +0800

    first commit                                 # 本次版本描述
```

> git-log - Show commit logs

```bash
 $ git log                                       # 显示所有 commit 的版本的详细信息

 $ git log --pretty=oneline                      # 显示 commit 版本的简略信息
 > 38c1df5cf2bed00f5b7365ee4913916e25238009 (HEAD -> master) first commit
 > d2716b6e88567c3fdfe390580d48ca82b581c04f (HEAD -> master) add file and line
```

> git-reset - Reset current HEAD to the specified state

通过 `git log` 定位回退的版本, 使用 `git reset` 执行回退

```bash
 $ git reset --hard HEAD <commit number>         # 回到指定 commit 版本

 $ git reset --hard HEAD^                        # 回退到上个版本
 > HEAD is now at d2716b6 add file and line
```

> git-diff - Show changes between commits, commit and working tree, etc

未被追踪(not ADD)和已追踪(ADD)内容进行比对, 已被追踪则不比对

```bash
 $ git diff
 > diff --git a/file b/file                      # file 已 ADD 和未 ADD 比对
 > index 08fe272..06fcdd7 100644
 > --- a/file                                    # - 开头是被追踪的内容
 > +++ b/file                                    # + 开头是未被追踪的内容
 > @@ -1 +1,2 @@
 > first line
 > +second line                                  # 新增行
```

> git-push - Update remote refs along with associated objects

将本地所有新增的 commit 推送到远端仓库

```bash
 $ git push origin master                        # 提交所有 commit 到 origin 仓库的 master 分支
 $ git push -u origin master                     # 将 origin 仓库 master 分支作为拉取和推送的默认值

 $ git push <repo> <branch>                      # 使用过 -u 后可以省略仓库和分支
 > To github.com:facser/Learning.git
   309ae9f..74beac3  main -> main
```
