## Git 进阶功能笔记

### cherry-pick

对于多分支的代码库，将代码从一个分支转移到另一个分支是常见需求。

这时分两种情况。一种情况是，你需要另一个分支的所有代码变动，那么就采用合并（`git merge`）。另一种情况是，你只需要部分代码变动（某几个提交），这时可以采用 Cherry pick。

#### 基本用法

```shell
git cherry-pick <commitHash>
```

上面命令就会将指定的提交`commitHash`，应用于**当前分支**。

#### 转移多个提交

cherry-pick 支持一次转移多个提交

```shell
git cherry-pick <HashA> <HashB>
```

#### 配置项

`git cherry-pick` 命令的常用配置项如下：

1. `-e`,`--edit`

   打开外部编辑器，编辑提交信息

2. `-n`,`--no-commit`

   只更新工作区和暂存区，不产生新的提交

3. `-x` 🌟

   在提交信息的末尾追加一行`(cherry picked from commit ...)` ，方便以后查到这个提交是如何产生的。

4. `-s`,`--signoff`

   在提交信息的末尾追加一行操作者的签名，表明是谁进行了这个操作

#### 代码冲突

如果操作过程中发生代码冲突，Cherry pick 会停下来，让用户决定如何继续操作。

1. `--continue`

   用户解决代码冲突后，第一步将修改的文件重新加入暂存区（`git add .`)，第二步使用下面的命令，让Cherry pick 过程继续执行

   ```shell
   git cherry-pick --continue
   ```

2. `--abort`

   发生代码冲突后，放弃合并，回到操作前的样子

3. `--quit`

   发生代码冲突后，退出 Cherry pick，但是不回到操作前的样子。

### Github 删除某次 commit

> 有时我们提交了错误的代码，需要撤销某次的 commit 记录，三种方式。

#### git reset

- `git reset` :回滚到某次提交
- `git reset --soft` ：此次提交之后的修改会被退回到暂存区
- `git reset --hard`： 此次提交之后的修改不做任何保留，`git status`查看工作区是没有记录的。

1. 回滚代码
    如果需要删除的 commit 是最新的，那么可以通过 `git reset` 命令将代码回滚到之前某次提交的状态，但一定要将现有的代码做好备份，否则回滚之后这些变动都会消失。具体操作如下：

   ```cpp
   git log // 查询要回滚的 commit_id
   git reset --hard commit_id // HEAD 就会指向此次的提交记录
   git push origin HEAD --force // 强制推送到远端
   ```

2. 误删恢复
    如果回滚代码之后发现复制错了 commit_id，或者误删了某次 commit 记录，也可以通过下方代码恢复：

   ```cpp
   git relog // 复制要恢复操作的前面的 hash 值
   git reset --hard hash // 将 hash 换成要恢复的历史记录的 hash 值
   ```

3. 注意：删除中间某次提交时最好不要用 `git reset` 回退远程库，因为之后其他人提交代码时用 `git pull` 也会把自己的本地仓库回退到之前的版本，容易出现差错进而增加不必要的工作量。

