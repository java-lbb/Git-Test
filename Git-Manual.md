# Git 操作手册



#### HEAD 概念

1. 在 Git 中，`HEAD` 是一个特殊的指针，它**指向当前正在工作的分支的最新提交**。换句话说，`HEAD` 是你当前工作目录的快照的引用。

2. 当你切换分支时，`HEAD` 会改变以指向新分支的最新提交。当你创建新的提交时，`HEAD` 会自动移动以指向新的提交。

3. `HEAD` 通常指向一个分支名，例如 `main` 或 `dev`。在这种情况下，我们说 `HEAD` 是"附着的"，因为它通过分支名间接引用提交。当你查看 `HEAD` 的内容时，你会看到它包含的是一个分支名。

4. 然而，`HEAD` 也可以直接指向一个提交。在这种情况下，我们说 `HEAD` 是"分离的"。这通常发生在你检出一个特定的提交而不是分支时。在这种情况下，当你查看 `HEAD` 的内容时，你会看到它包含的是一个提交的哈希值。

5. 总的来说，`HEAD` 在 Git 中是一个非常重要的概念，它帮助你知道你当前在哪个分支上工作，以及你的工作目录对应的是哪个提交。

#### 一、git restore

`git restore` 命令是 Git 版本控制系统中用于恢复工作目录文件的一个工具。它可以用来撤销对文件的修改，将文件恢复到上一个提交的状态，或者将暂存区的文件恢复到工作目录。

`git restore` 这个命令默认只作用于 Git 跟踪（track）的文件，需要一个历史状态作为参考来恢复文件，而对于一个全新的、未被 Git 跟踪的文件，Git 没有任何历史记录可以参考。

这里是一些使用 `git restore` 的常见场景和示例：

1. **撤销工作目录中文件的修改**：
   如果你对文件做了修改，但还没有暂存（也就是没有执行 `git add`），你可以使用 `git restore` 将它们恢复到上次提交时的状态。

   ```bash
   git restore <file>
   ```

2. **从暂存区中移除文件**：
   如果你已经使用 `git add` 将文件添加到暂存区，但想要撤销这一操作，但不会改变工作区的文件。可以使用 `git restore --staged` 将文件从暂存区移回工作目录。

   ```bash
   git restore --staged <file>
   ```

   

3. **恢复到某个特定的提交**：
   如果你想要将文件恢复到特定的提交，可以指定提交的哈希值。

   ```bash
   git restore --source=<commit-hash> <file>
   ```

4. **恢复整个工作目录**：
   如果你想要将整个工作目录恢复到最新的提交状态，可以使用以下命令：

   ```bash
   git restore .
   ```

> 请记住，`git restore` 不会影响已经提交的历史，它只影响工作目录和暂存区。此外，使用 `git restore` 时要小心，因为你对文件所做的任何未提交的更改都会被永久丢弃。在执行这个命令之前，确保你不需要这些未提交的更改。



#### 二、git checkout 

基本用法如下：

1. **切换分支**：`git checkout <branch-name>`
   这将会切换到指定的分支。
2. **创建并切换到新分支**：`git checkout -b <new-branch-name>`
   这将会创建一个新的分支，并切换到新创建的分支。
3. **恢复文件到最近一次 commit 的状态**：`git checkout -- <file>`
   这将会恢复指定文件到最近一次 commit 的状态。

例如：

```bash
# 切换到已存在的分支 "develop"
git checkout develop

# 创建并切换到新分支 "feature-x"，这个功能可以通过 git branch <new-branch> 和 git switch <new-branch> 两个命令来实现。
git checkout -b feature-x

# 恢复文件 "a.txt" 到最近一次 commit 的状态，这个功能可以通过 git restore <file>（在 Git 2.23 及以后版本中可用）来实现。
git checkout -- a.txt

# HEAD~1 命令的作用是将文件 a.txt 恢复到相对于 HEAD 的上一次提交的状态。
# -- 是一个分隔符，用来区分命令行参数和文件列表。这在文件名可能与分支名或标签名冲突的情况下特别有用。如果你有一个名为 HEAD 的文件，你可以使用 git checkout -- HEAD 来检出这个文件，而不是检出名为 HEAD 的分支或标签。
# ③ 所以，git checkout HEAD~1 -- a.txt 命令的意思是：“检出（或者说恢复）文件 a.txt 到相对于 HEAD 的上一次提交的状态”，这将会撤销你对 a.txt 文件在工作区中所做的所有未提交的更改。

git checkout HEAD~1 -- a.txt 

# 检出特定的提交：git checkout <commit-hash> 可以检出特定的提交，<commit-hash> 是你想要检出的特定提交的哈希值, 进入所谓的 "分离 HEAD" 状态。 这个功能可以通过 git switch --detach <commit-hash>（在 Git 2.23 及以后版本中可用）来实现。
git checkout <commit-hash>
```

> 请注意，`git checkout` 命令在 Git 2.23 版本后被拆分为两个新的命令：`git switch` 和 `git restore`，以使命令的目的更加明确。



**什么是 "分离HEAD"**

```
当你处于 "分离 HEAD" 状态时，你的 HEAD 指向的是一个具体的提交，而不是一个分支。这意味着你可以在工作目录中自由地修改文件、添加新文件或删除文件，这些更改不会影响到任何已存在的分支。

举个例子，假设你执行了 git checkout c4d7fb6343e36af2a422c85e93515e627345a244 进入了 "分离 HEAD" 状态，然后你修改了一个文件 file1.txt。这个修改不会影响到 main 分支或任何其他分支的 file1.txt 文件。这是因为你的更改只存在于你当前的工作目录中，而并没有被提交到任何分支。

然而，如果你在 "分离 HEAD" 状态下做了一些更改，然后想要保存这些更改，你需要创建一个新的分支，然后将这些更改提交到新的分支。如果你不这样做，而是直接切换到另一个分支，那么你在 "分离 HEAD" 状态下做的更改将会丢失。
```



#### 三、git branch 

`git branch` 命令在 Git 中用于处理与分支相关的操作。以下是一些常见的用法：

1. **查看分支**：如果不带任何参数地运行 `git branch`，它会列出你的本地仓库中所有的分支。当前活动的分支（即 `HEAD` 指向的分支）会被标记出来。

```bash
git branch
```

2. **创建分支**：如果你想创建一个新的分支，你可以使用 `git branch` 命令后跟上你想要的新分支的名字。例如，以下命令会创建一个名为 `new-branch` 的新分支：

```bash
# 默认基于当前 HEAD 所指向的提交处创建一个名为 new-branch 的新分支。这里的 HEAD 通常指的是你当前所在的分支的最新提交。
git branch new-branch
# 基于某次提交的hash创建新分支
git branch new-branch <commit-hash>
```

3. **删除分支**：如果你想删除一个分支，你可以使用 `git branch -d` 命令后跟上你想要删除的分支的名字。例如，以下命令会删除名为 `old-branch` 的分支：

```bash
git branch -d old-branch
```

4. **强制修改分支指向**

```bash
# 上面的命令会将某分支强制指向某次提交
git branch -f <branch-name> <commit-hash>
# 1. main 分支会被强制指向到当前 HEAD 的三个提交之前。
git branch -f main HEAD~3
# 2. main 分支会被强制指向到当前 哈希值为 afe4dg45 的提交
git branch -f main afe4dg45
```



> 请注意，你不能删除当前活动的分支。如果你想删除一个分支，你需要先切换到另一个分支



#### 四、git switch

`git switch` 是 Git 2.23 版本中引入的新命令，用于**切换分支**。它是 `git checkout` 命令的一个更直观的替代品，因为 `git checkout` 命令的功能过于复杂，既可以用来切换分支，也可以用来恢复文件。

以下是 `git switch` 的一些基本用法：

- 切换到已存在的分支：`git switch <branch-name>`
- 创建新分支并切换到新分支：`git switch -c <new-branch-name>`
- 切换到上一个分支：`git switch -`



#### 五、git merge

`git merge` 是用于合并两个或多个提交历史的 Git 命令。它通常用于将一个或多个分支的更改合并到当前分支。

以下是 `git merge` 的一些基本用法：

1. 合并一个分支到当前分支：`git merge <branch-name>`
2. 使用不同的合并策略：`git merge --strategy=<strategy> <branch-name>`
3. 在合并时创建一个新的合并提交：`git merge --no-ff <branch-name>`

例如，假设你在 `master` 分支上，并且你想要合并 `feature` 分支的更改，你可以这样做：

```bash
git checkout master
git merge feature
```

当你运行 `git merge` 命令合并两个分支时，Git 会创建一个新的提交，这个提交有两个父提交：一个是你正在工作的分支的最新提交，另一个是你想要合并的分支的最新提交。然后 Git 会移动当前分支的HEAD指针，使其指向这个新的合并提交。

如果两个分支在同一文件的同一位置进行了更改，Git 将无法自动合并这些更改，这将导致合并冲突。在这种情况下，你需要手动解决这些冲突。

##### 正常merge例子

1. 假设你有两个分支，`main` 和 `future`，它们都有各自的新提交。在这个例子中，我们将使用字母来表示各个提交，如下所示：

```
A - B - C (main)
     \
      D - E (future)
```

在这个例子中，`main` 分支的最新提交是 `C`，`future` 分支的最新提交是 `E`。

2. 当你将 `future` 分支合并到 `main` 分支时，Git 会创建一个新的合并提交，这个提交有两个父提交：一个是 `main` 分支的最新提交 `C`，又一个是 `future` 分支的最新提交 `E`。然后 Git 会移动 `main` 分支的指针，使其指向这个新的合并提交。我们将这个新的合并提交标记为 `F`，如下所示：

```
A - B - C - F (main)
     \       /
      D - E (future)
```

3. 接下来，如果你再次将 `main` 分支合并到 `future` 分支，由于 `future` 分支已经包含了 `main` 分支的所有提交（因为你刚刚将 `future` 分支合并到了 `main` 分支），所以实际上没有任何新的更改需要合并。在这种情况下，Git 不会创建新的合并提交，而是简单地将 `future` 分支的指针移动到 `main` 分支的最新提交 `F`，如下所示：

```
A - B - C - F (main, future)
     \       /
      D - E
```

在这个例子中，`main` 和 `future` 分支现在都指向同一个提交 `F`，这意味着它们完全同步了。

##### 冲突例子

若产生冲突, 通常发生在两个或更多的开发者修改了同一文件的同一部分，然后尝试将这些更改合并到一起。

解决冲突的基本步骤如下：

1. 首先，你需要找出哪些文件有冲突。你可以通过运行 `git status` 命令来查看这些文件。

```bash
git status
```

这将列出所有有冲突的文件。

2. 接下来，你需要打开这些文件并查找以下标记：

```bash
<<<<<<< HEAD
your changes
=======
other changes
>>>>>>> branch-name
```

在这些标记之间的是冲突的内容。`<<<<<<< HEAD` 和 `=======` 之间的是你的更改，`=======` 和

`>>>>>>> branch-name` 之间的是其他分支的更改。

3. 你需要决定如何解决这些冲突。你可以选择保留你的更改，保留其他分支的更改，或者手动合并这些更改。一旦你做出决定，就可以删除这些标记，并保留你想要的内容。

4. 保存文件并退出编辑器。

5. 一旦你解决了所有的冲突，你需要将这些更改添加到暂存区：

```bash
git add .
```

6. 然后，你可以提交这些更改：

```bash
git commit -m "Resolved merge conflicts"
```

关于 merge 之后的分离HEAD指向问题

当你在 `main` 分支上执行 `merge bugFix` 操作后，会生成一个新的合并提交。这个合并提交有两个父提交，一个是 `main` 分支最后的提交（假设为 `c`），另一个是 `bugFix` 分支的最后提交（假设为 `d`）。

在 Git 中，`^` 符号用于引用父提交。如果一个提交有多个父提交，那么 `^` 后面可以跟一个数字来指定具体的父提交。例如，`main^1` 指的是 `main` 的第一个父提交，`main^2` 指的是 `main` 的第二个父提交。

如果 `^` 后面没有数字，那么 Git 默认引用的是第一个父提交。所以，当你执行 `git checkout main^` 的时候，Git 会将 `HEAD` 指向 `main` 的第一个父提交，即 `c`。

如果你想让 `HEAD` 指向 `main` 的第二个父提交，即 `d`，你可以执行 `git checkout main^2`。



#### 六、git rebase

`git rebase` 是一个强大的工具，它可以将一系列提交应用到一个新的基础上。它的主要用途是保持一个干净的提交历史，因为它可以用来将多个提交合并为一个，或者改变提交的顺序。

以下是 `git rebase` 的基本用法：

```bash
git rebase <base>
```

在这个命令中，`<base>` 可以是任何可以被 Git 解析为提交的东西，例如一个分支名、一个标签名、或者一个提交的哈希值。

现在，让我们通过一个例子来解释 `git rebase` 的工作原理。假设你有以下的提交历史：

```
A - B - C (main)
     \
      D - E (feature)
```

在这个例子中，`main` 分支的最新提交是 `C`，`feature` 分支的最新提交是 `E`。

现在，假设你想将 `feature` 分支的更改应用到 `main` 分支的最新提交上。你可以在 `feature` 分支上运行以下命令：

```bash
git rebase main
```

这个命令会做以下的事情：

1. 找到 `main` 和 `feature` 分支的共同祖先，也就是提交 `B`。
2. 计算从 `B` 到 `E` 的每个提交引入的更改。这些更改被称为补丁。
3. 检出 `main` 分支的最新提交 `C`，创建一个新的临时分支。
4. 在这个新的临时分支上应用之前计算出的补丁。

结果会是这样的：

```
A - B - C (main)
     \   \
      \    D' - E' (feature)
       \
        D - E
```

在这个例子中，`D'` 和 `E'` 是原来 `D` 和 `E` 提交的复制品，但是它们是基于 `C` 而不是 `B`。

注意，`git rebase` 会改变提交历史。在上面的例子中，`D` 和 `E` 的哈希值会改变，因为它们现在是基于 `C` 而不是 `B`。这就是为什么你应该只在你的本地分支上使用 `git rebase`，而不是在公共分支上使用。



#### 七、git reset 

`git reset` 是一个强大的命令，用于撤销更改。它有三种模式：`--soft`，`--mixed`，`--hard`，这些模式决定了 `git reset` 命令的行为。

1. `git reset --soft <commit-hash>`：移动 `HEAD` 指针到指定的 `<commit>`，但是不改变索引（暂存区）和工作目录。这意味着所有的更改都会被标记为 "**已暂存**"。

   ##### git reset --soft 例子

   ```bash
   git reset --soft 是一个非常有用的命令，它会移动 HEAD 指针到指定的提交，但不会更改索引或工作目录。这意味着你的文件不会被改变，但 Git 会认为你回到了一个早期的提交。这在你想改变最近的提交时非常有用。
   
   假设你的提交历史如下：
   
   a1b2c3d (HEAD -> main) commit 3
   e4f5g6h commit 2
   i7j8k9l commit 1
   现在，你想更改 "commit 3"，但不想更改任何文件。你可以使用 git reset --soft 来做到这一点：
   
   git reset --soft e4f5g6h
   现在，HEAD 指向 "commit 2"，但你的文件没有被改变。如果你运行 git status，你会看到 "commit 3" 的更改被标记为待提交。你可以使用 git commit 来创建一个新的提交，这个新的提交可以有不同的提交消息，也可以包含不同的文件更改。
   
   git commit -m "New commit 3"
   现在，你的提交历史看起来像这样：
   
   a2b3c4d (HEAD -> main) New commit 3
   e4f5g6h commit 2
   i7j8k9l commit 1
   "New commit 3" 包含了原来 "commit 3" 的所有更改，但它有一个新的提交哈希和一个新的提交消息。
   ```

   

2. `git reset --mixed <commit-hash>`：移动 `HEAD` 指针到指定的 `<commit>`，并且改变索引以匹配 `<commit>`，但是不改变工作目录。这是 `git reset` 的默认模式。这意味着所有的更改都会被标记为 "**未暂存**"。

   ##### git reset --mixed 例子

   ```bash
   a1b2c3d (HEAD -> main) commit 3
   e4f5g6h commit 2
   i7j8k9l commit 1
   ```

   比如我在 commit 3 中对其新建的 main.txt 文件增加了一行"aaa"，我也执行了 `git add main.txt` 命令添加到暂存区中，然后再添加一行 "bbb"，但是"bbb"未添加到暂存区。

   但是当我执行 `git reset --mixed HEAD~1/HEAD^` 之后，main.txt 文件之前的内容包括我刚刚添加到暂存区的 "aaa" 都会重置为 "未暂存"的状态，原来工作区发生变更的内容仍然存在，即 "bbb" 保持不变。

   ```bash
   # 这个命令将 `HEAD` 指针移动到 "commit 2"，并将索引重置为 "commit 2" 的状态。如果你现在运行 `git status`，你会看到你的更改仍然存在，但是它们已经被取消暂存，现在处于未暂存状态。
   git reset --mixed HEAD~1
   ```

   可以选择重新暂存这些更改，即执行 `git add . `  

   

3. `git reset --hard <commit-hash>`：移动 `HEAD` 指针到指定的 `<commit>`，并且改变索引和工作目录以匹配 `<commit>`。这意味着所有的更改都会被丢弃。

   ##### git reset --hard 例子

   假设你有以下的提交历史（最新的提交在顶部）：

   ```bash
   e5bb5b4 (HEAD -> main) add feature X
   a035091 fix bug Y
   7a5e5a3 initial commit
   ```

   如果你运行 `git reset --hard a035091`，你的提交历史将会变成：

   ```bash
   a035091 (HEAD -> main) fix bug Y
   7a5e5a3 initial commit
   ```

   "add feature X" 的提交被丢弃了，`HEAD` 和 `main` 分支现在都指向 "fix bug Y" 的提交。

   请注意，`git reset` 是一个危险的命令，特别是 `--hard` 模式，因为它会丢弃更改。

   ##### git reset --hard 丢失的提交恢复

   如果你不小心执行了 `git reset --hard` 并丢失了最新的提交，你可以使用 `git reflog` 命令来找回它。`git reflog` 显示了 `HEAD` 指针的移动历史，这意味着你可以看到你的提交历史，包括被 `git reset` 丢弃的提交。

   下面是如何使用 `git reflog` 找回丢失的提交：

   - 运行 `git reflog` 命令。你会看到一个列表，显示了 `HEAD` 指针的历史移动。每一行都表示一次 `HEAD` 的移动，最新的移动在顶部。每一行都包含一个提交的哈希值和一个描述 `HEAD` 如何移动的简短消息。

     ```bash
     e5bb5b4 (HEAD -> main) HEAD@{0}: reset: moving to a035091
     a035091 HEAD@{1}: commit: fix bug Y
     7a5e5a3 HEAD@{2}: commit (initial): initial commit
     ```

   - 找到你丢失的提交。在这个例子中，丢失的提交是 "add feature X"，它的哈希值是 `e5bb5b4`。

   - 使用 `git reset --hard <commit>` 命令将 `HEAD` 指针移动回丢失的提交。在这个例子中，你应该运行 `git reset --hard e5bb5b4`。

     ```bash
     e5bb5b4 (HEAD -> main) add feature X
     a035091 fix bug Y
     7a5e5a3 initial commit
     ```

     现在，`HEAD` 和 `main` 分支都指向 "add feature X" 的提交，就像你从未运行 `git reset --hard a035091` 一样。请注意，`git reflog` 是一个强大的工具，但也是一个危险的工具。在使用它之前，你应该确保你理解它的工作原理。



#### 八、git revert

`git revert` 是一个用于撤销之前的提交的命令。它会创建一个新的提交，这个新的提交会撤销指定的提交。被撤销的提交的更改将被反向应用，然后在新的提交中记录下来。这是一个安全的命令，因为它不会改变历史记录。

这是一个基本的使用 `git revert` 的例子：

```bash
git revert <commit>
```

在这里，`<commit>` 是你想要撤销的提交的哈希值。这将打开一个文本编辑器，让你输入一个提交消息。默认的提交消息通常是足够的，它会指明这个提交是撤销哪个提交的。保存并关闭编辑器后，一个新的提交将被创建。

如果你想要撤销多个提交，你可以在 `git revert` 命令后面列出所有你想要撤销的提交的哈希值：

```bash
git revert <commit1> <commit2> <commit3>
```

这将会为每个被撤销的提交创建一个新的提交。

如果你想要撤销一系列的提交，你可以使用 `..` 符号：

```bash
git revert <older-commit>..<newer-commit>
```

注意，这个命令会撤销在 `<older-commit>` 和 `<newer-commit>` 之间的所有提交，但是不包括 `<older-commit>` 本身。

`git revert` 是一个非常有用的命令，它允许你安全地撤销之前的提交，而不需要改变项目的历史。



#### 九、git cherry-pick

`git cherry-pick` 是一个强大的命令，它可以将某个分支的提交应用到当前分支。这在你想要将特定的更改从一个分支移动到另一个分支时非常有用。

基本的 `git cherry-pick` 命令的使用方法如下：

```bash
git cherry-pick <commit-hash>
```

这里的 `<commit-hash>` 是你想要应用的提交的哈希值。你可以从 `git log` 命令的输出中找到这个值。

例如，如果你有一个提交的哈希值是 `abc123`，你可以使用以下命令将这个提交应用到当前分支：

```bash
git cherry-pick abc123
```

这将会创建一个新的提交，这个提交的更改与 `abc123` 提交的更改相同，但是它有一个新的哈希值和提交时间。

如果你想要应用多个提交，你可以在命令行中列出它们，例如：

```bash
git cherry-pick abc123 def456
```

这将会创建两个新的提交，分别对应 `abc123` 和 `def456` 提交的更改。

注意，`git cherry-pick` 只会应用提交的更改，而不会改变分支的历史。如果你想要将整个分支的历史合并到当前分支，你应该使用 `git merge` 或 `git rebase` 命令。

下面是一个 `git cherry-pick` 的示意图：

```
A---B---C topic
     \
      D---E---F---G master
```

如果我们在 master 分支上执行 `git cherry-pick C`，那么会得到：

```
A---B---C topic
     \
      D---E---F---G---C' master
```

在这个图中，`C'` 是 `C` 提交的副本，它包含了 `C` 提交的更改，但是有一个新的哈希值和提交时间。