
## 1. Git 三大区域

- **工作区（Working Directory）**  
  - 指你在电脑上看到并编辑的实际目录和文件。  
  - 所有对文件的修改最初都发生在这里。

- **暂存区（Staging Area / Index）**  
  - 位于 Git 仓库的隐藏目录（.git）中，主要存放在文件 `.git/index` 里。  
  - 用于临时保存将要提交到版本库的文件快照。  
  - “add” 操作就是将工作区中的修改放入暂存区。

- **版本库（Repository）**  
  - 就是 `.git` 目录，包含 Git 对所有提交历史、配置、对象等信息的存储。  
  - 版本库记录了项目的所有版本信息，但不直接显示在工作区中。

---

## 2. 常见提交流程

1. **查看状态**  
   - 命令：`git status`  
   - 作用：查看工作区、暂存区和版本库的当前状态。

2. **添加到暂存区**  
   - 命令：`git add .` 或 `git add <file>`  
   - 作用：将工作区中的改动添加到暂存区，为下一步提交做准备。

3. **提交到本地仓库**  
   - 命令：`git commit -m "修复XXbug"`  
   - 作用：将暂存区中的内容提交到版本库中，形成一个新的提交记录。

4. **更新远程仓库前合并最新更改**  
   - 命令：`git pull <远程主机名> <远程分支名>`  
   - 说明：相当于先执行 `git fetch` 再执行 `git merge`。若有冲突需要解决后再提交。

5. **推送到远程仓库**  
   - 命令：`git push <远程主机名> <远程分支名>`（如 `git push origin master`）  
   - 作用：将本地提交的更新推送到远程仓库。

---

## 3. 分支管理与操作

- **创建分支**  
  - 单独创建：`git branch <name>`  
  - 创建并切换：  
    - 老写法：`git checkout -b <name>`（相当于先 `git branch <name>` 再 `git checkout <name>`）  
    - 新写法：`git switch -c <name>`

- **切换分支**  
  - 命令：`git checkout <name>` 或 `git switch <name>`

- **查看分支**  
  - 命令：`git branch`

- **合并分支**  
  - 命令：`git merge <name>`  
  - 说明：将指定分支的修改合并到当前分支，通常会生成一个合并提交（merge commit），其有两个父提交。

- **删除分支**  
  - 本地删除：`git branch -d <name>`（若分支未合并，会提示错误，可用 `-D` 强制删除）  
  - 远程删除：  
    - 命令1：`git push origin :<remote-branch>`  
    - 命令2：`git push origin --delete <remote-branch>`

- **HEAD 与版本跳转**  
  - HEAD 指向当前检出的版本。  
  - 通过 `git reset --hard <commit_id>` 可以将当前分支重置到某个提交（注意此操作会丢弃工作区和暂存区的修改）。

---

## 4. 撤销修改和临时保存

- **撤销工作区修改**  
  - 如果文件未加入暂存区：`git checkout -- <file>` 可以丢弃工作区的改动。

- **撤销暂存区的更改**  
  - 命令：`git reset HEAD <file>`  
  - 说明：把文件从暂存区中移除，恢复到仅在工作区修改的状态，再使用 `git checkout -- <file>` 丢弃改动。

- **暂存现场（Stash）**  
  - 命令：`git stash` 将当前工作现场保存起来，清空工作区。  
  - 命令：`git stash pop` 恢复之前保存的现场。  
  - 用途：当手头工作未完成，但需要切换分支处理其他紧急问题时非常有用。

---

## 5. 其他高级操作

- **Cherry-Pick**  
  - 命令：`git cherry-pick <commit>`  
  - 说明：将其他分支中某个（或一系列）特定提交的改动复制到当前分支。  
  - 注意：示例中“git cherry-pick feature”容易引起误解，因为 cherry-pick 通常需要一个具体的 commit ID，而非分支名称。如果要复制分支中最近一次提交，可以先确定该提交的 commit hash，再使用 cherry-pick。

- **Reset**  
  - 命令：`git reset --hard <commit_id>`  
  - 说明：硬重置会同时更新当前分支指针、暂存区和工作区，丢弃所有未提交的修改。  
  - 警告：若该提交已推送到远程仓库，建议使用 `git revert` 来撤销提交，以免破坏公共历史。

- **Merge 与 Rebase**  
  - **Merge**：  
    - 命令：`git merge <branch>`  
    - 说明：合并两个分支会生成一个新的合并提交，保留两条分支的历史。  
  - **Rebase**：  
    - 命令：`git rebase <upstream>`  
    - 说明：将当前分支的提交摘下来，重新应用到指定的基线之上，从而使提交历史更加线性。  
    - 注意：对公共分支使用 rebase 可能会导致历史混乱，建议仅在个人分支上使用。

- **仓库镜像迁移**  
  - 迁移整个仓库到新仓库（包括所有分支、标签等）可使用 `--mirror` 参数：  
    1. 克隆为裸仓库：  
       ```bash
       git clone --mirror https://github.com/user/old-repo.git
       ```
    2. 进入裸仓库目录：  
       ```bash
       cd old-repo.git
       ```
    3. 添加新远程仓库：  
       ```bash
       git remote add new-origin https://github.com/user/new-repo.git
       ```
    4. 推送所有引用到新远程仓库：  
       ```bash
       git push --mirror new-origin
       ```

---

## 6. 多人协作常见工作流程

- **推送与拉取**  
  1. 开发者在本地提交后，使用 `git push origin <branch-name>` 推送到远程分支。  
  2. 若推送失败（通常因远程分支有更新），先执行 `git pull origin <branch-name>` 将远程更改合并到本地，解决冲突后再推送。  
  3. 如果提示“no tracking information”，可用命令设置跟踪分支：  
     ```bash
     git branch --set-upstream-to=origin/<branch-name>
     ```
  
- **处理冲突**  
  - 当合并或拉取过程中出现冲突时，手动编辑冲突文件，标记解决后再执行 `git add` 和 `git commit` 完成合并。

---

# git

**Git reset/revert:**

git reset 用于将当前分支的HEAD指针和索引重置到指定的提交。它有三种模式：--soft（仅重置HEAD）、--mixed（重置HEAD和索引）和--hard（重置HEAD、索引和工作目录）。git revert 用于创建一个新的提交，撤销指定的历史提交，而不会改变提交历史。

**Merge/rebase:**

git merge 用于将另一个分支的更改合并到当前分支，生成一个新的合并提交。git rebase 用于将当前分支的更改应用到另一个分支的顶端，从而创建一个线性的提交历史。

**Pull/fetch:**

git pull 是 git fetch 和 git merge 的组合命令，它从远程仓库获取更新并合并到当前分支。git fetch 则仅从远程仓库获取更新，但不进行自动合并。

**git迁移项目到其他代码仓库，且保留分支与提交记录:**

指使用 Git 的工具和命令将一个项目从一个代码仓库迁移到另一个仓库，并保留所有的分支和提交历史记录。这通常涉及使用 git clone --mirror 和 git push --mirror 命令。


## 参考资料

- [CS-Notes/Git.md · CyC2018/CS-Notes (GitHub)](https://github.com/CyC2018/CS-Notes/blob/master/git/Git.md)
- [Rogerdudler Git Guide (中文)](http://rogerdudler.github.io/git-guide/index.zh.html)
- [Git Flight Rules (中文)](https://github.com/k88hudson/git-flight-rules/blob/master/README_zh-CN.md)
- [Git Cherry-Pick 命令指南](https://deepinout.com/git/git-tutorial/19_git_cherry-pick_command__a_comprehensive_guide_to_its_usage.html)
- [Git Reset 介绍](https://initialcommit.com/blog/git-reset)
- [Merge vs Rebase 解析](https://blog.csdn.net/u014600626/article/details/108690049)

---
