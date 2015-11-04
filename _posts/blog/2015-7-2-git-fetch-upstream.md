---
layout: post
title: 如何在 github fork 分支同步源分支修订
description: 在 github 上 fork 分支之后，需要周期性地对 fork 分支进行源分支同步，以确保源分支的 Bug fix 或 new feature 在 fork 分支上得更新。
category: blog
---


在 github 上 fork 分支之后，需要周期性地对 fork 分支进行源分支同步，以确保源分支的 Bug fix 或 new feature 在 fork 分支上得更新，具体操作分为两个步骤：

1.  为 fork 分支配置同步源分支。
2.  在 fork 分支上进行源分支修订同步。

## 配置源分支

1.  打开终端工具(对于 MAC 用户）或命令行窗口（Windows/Linux用户）
2.  查看本地库配置

<pre>$git remote -v
origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
origin  https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
</pre>

1.  为本地 fork 分支库配置源分支路径为 upsteam .

<pre>$git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
</pre>

1.  查看本地库配置，确认步骤3已正确配置

<pre>$git remote -v
origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (fetch)
origin    https://github.com/YOUR_USERNAME/YOUR_FORK.git (push)
upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (fetch)
upstream  https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git (push)
</pre>

## 同步源分支修订

1.  打开终端工具(对于 MAC 用户）或命令行窗口（Windows/Linux用户）
2.  进入 fork 分支的本地目录
3.  从 upstream/master 同步更新到本地 master 分支

<pre>$git fetch upstream
 remote: Counting objects: 75, done.
 remote: Compressing objects: 100% (53/53), done.
 remote: Total 62 (delta 27), reused 44 (delta 9)
 Unpacking objects: 100% (62/62), done.
 From https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY
  * [new branch]      master     -> upstream/master
</pre>

4\.checkout 本地 master 分支

<pre>$git checkout master
Switched to branch 'master'
</pre>

5\.合并 upstream/master 分支更新到本地的 master 分支

<pre>$git merge upstream/master
 Updating a422352..5fdff0f
 Fast-forward
  README                    |    9 -------
  README.md                 |    7 ++++++
  2 files changed, 7 insertions(+), 9 deletions(-)
  delete mode 100644 README
  create mode 100644 README.md
</pre>

如果本地分支原本没有任何修订，git 会`fast-forward`

<pre>$git merge upstream/master
 Updating 34e91da..16c56ad
 Fast-forward
  README.md                 |    5 +++--
  1 file changed, 3 insertions(+), 2 deletions(-)
</pre>

* * *

原文地址:

*   [Configuring a remote for a fork][1]
*   [Syncing a fork][2]

 [1]: https://help.github.com/articles/configuring-a-remote-for-a-fork/
 [2]: https://help.github.com/articles/syncing-a-fork/

