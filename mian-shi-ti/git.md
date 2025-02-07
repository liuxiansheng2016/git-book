# git

**Git reset/revert:**

git reset 用于将当前分支的HEAD指针和索引重置到指定的提交。它有三种模式：--soft（仅重置HEAD）、--mixed（重置HEAD和索引）和--hard（重置HEAD、索引和工作目录）。git revert 用于创建一个新的提交，撤销指定的历史提交，而不会改变提交历史。

**Merge/rebase:**

git merge 用于将另一个分支的更改合并到当前分支，生成一个新的合并提交。git rebase 用于将当前分支的更改应用到另一个分支的顶端，从而创建一个线性的提交历史。

**Pull/fetch:**

git pull 是 git fetch 和 git merge 的组合命令，它从远程仓库获取更新并合并到当前分支。git fetch 则仅从远程仓库获取更新，但不进行自动合并。

**git迁移项目到其他代码仓库，且保留分支与提交记录:**

指使用 Git 的工具和命令将一个项目从一个代码仓库迁移到另一个仓库，并保留所有的分支和提交历史记录。这通常涉及使用 git clone --mirror 和 git push --mirror 命令。
