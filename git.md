# git

### 1. Git 三大区域 <a href="#id-1.-git-san-da-qu-yu" id="id-1.-git-san-da-qu-yu"></a>

* **工作区（Working Directory）**
  * 指你在电脑上看到并编辑的实际目录和文件。
  * 所有对文件的修改最初都发生在这里。
* **暂存区（Staging Area / Index）**
  * 位于 Git 仓库的隐藏目录（.git）中，主要存放在文件 `.git/index` 里。
  * 用于临时保存将要提交到版本库的文件快照。
  * “add” 操作就是将工作区中的修改放入暂存区。
* **版本库（Repository）**
  * 就是 `.git` 目录，包含 Git 对所有提交历史、配置、对象等信息的存储。
  * 版本库记录了项目的所有版本信息，但不直接显示在工作区中。

***

### 2. 常见提交流程 <a href="#id-2.-chang-jian-ti-jiao-liu-cheng" id="id-2.-chang-jian-ti-jiao-liu-cheng"></a>

1. **查看状态**
   * 命令：`git status`
   * 作用：查看工作区、暂存区和版本库的当前状态。
2. **添加到暂存区**
   * 命令：`git add .` 或 `git add <file>`
   * 作用：将工作区中的改动添加到暂存区，为下一步提交做准备。
3. **提交到本地仓库**
   * 命令：`git commit -m "修复XXbug"`
   * 作用：将暂存区中的内容提交到版本库中，形成一个新的提交记录。
4. **更新远程仓库前合并最新更改**
   * 命令：`git pull <远程主机名> <远程分支名>`
   * 说明：相当于先执行 `git fetch` 再执行 `git merge`。若有冲突需要解决后再提交。
5. **推送到远程仓库**
   * 命令：`git push <远程主机名> <远程分支名>`（如 `git push origin master`）
   * 作用：将本地提交的更新推送到远程仓库。

***

### 3. 分支管理与操作 <a href="#id-3.-fen-zhi-guan-li-yu-cao-zuo" id="id-3.-fen-zhi-guan-li-yu-cao-zuo"></a>

* **创建分支**
  * 单独创建：`git branch <name>`
  * 创建并切换：
    * 老写法：`git checkout -b <name>`（相当于先 `git branch <name>` 再 `git checkout <name>`）
    * 新写法：`git switch -c <name>`
* **切换分支**
  * 命令：`git checkout <name>` 或 `git switch <name>`
* **查看分支**
  * 命令：`git branch`
* **合并分支**
  * 命令：`git merge <name>`
  * 说明：将指定分支的修改合并到当前分支，通常会生成一个合并提交（merge commit），其有两个父提交。
* **删除分支**
  * 本地删除：`git branch -d <name>`（若分支未合并，会提示错误，可用 `-D` 强制删除）
  * 远程删除：
    * 命令1：`git push origin :<remote-branch>`
    * 命令2：`git push origin --delete <remote-branch>`
* **HEAD 与版本跳转**
  * HEAD 指向当前检出的版本。
  * 通过 `git reset --hard <commit_id>` 可以将当前分支重置到某个提交（注意此操作会丢弃工作区和暂存区的修改）。

***

### 4. 撤销修改和临时保存 <a href="#id-4.-che-xiao-xiu-gai-he-lin-shi-bao-cun" id="id-4.-che-xiao-xiu-gai-he-lin-shi-bao-cun"></a>

* **撤销工作区修改**
  * 如果文件未加入暂存区：`git checkout -- <file>` 可以丢弃工作区的改动。
* **撤销暂存区的更改**
  * 命令：`git reset HEAD <file>`
  * 说明：把文件从暂存区中移除，恢复到仅在工作区修改的状态，再使用 `git checkout -- <file>` 丢弃改动。
* **暂存现场（Stash）**
  * 命令：`git stash` 将当前工作现场保存起来，清空工作区。
  * 命令：`git stash pop` 恢复之前保存的现场。
  * 用途：当手头工作未完成，但需要切换分支处理其他紧急问题时非常有用。

***

### 5. 其他高级操作 <a href="#id-5.-qi-ta-gao-ji-cao-zuo" id="id-5.-qi-ta-gao-ji-cao-zuo"></a>

* **Cherry-Pick**
  * 命令：`git cherry-pick <commit>`
  * 说明：将其他分支中某个（或一系列）特定提交的改动复制到当前分支。
  * 注意：示例中“git cherry-pick feature”容易引起误解，因为 cherry-pick 通常需要一个具体的 commit ID，而非分支名称。如果要复制分支中最近一次提交，可以先确定该提交的 commit hash，再使用 cherry-pick。
* **Reset**
  * 命令：`git reset --hard <commit_id>`
  * 说明：硬重置会同时更新当前分支指针、暂存区和工作区，丢弃所有未提交的修改。
  * 警告：若该提交已推送到远程仓库，建议使用 `git revert` 来撤销提交，以免破坏公共历史。
* **Merge 与 Rebase**
  * **Merge**：
    * 命令：`git merge <branch>`
    * 说明：合并两个分支会生成一个新的合并提交，保留两条分支的历史。
  * **Rebase**：
    * 命令：`git rebase <upstream>`
    * 说明：将当前分支的提交摘下来，重新应用到指定的基线之上，从而使提交历史更加线性。
    * 注意：对公共分支使用 rebase 可能会导致历史混乱，建议仅在个人分支上使用。
* **仓库镜像迁移**
  * 迁移整个仓库到新仓库（包括所有分支、标签等）可使用 `--mirror` 参数：
    1.  克隆为裸仓库：

        Copy

        ```
        git clone --mirror https://github.com/user/old-repo.git
        ```
    2.  进入裸仓库目录：

        Copy

        ```
        cd old-repo.git
        ```
    3.  添加新远程仓库：

        Copy

        ```
        git remote add new-origin https://github.com/user/new-repo.git
        ```
    4.  推送所有引用到新远程仓库：

        Copy

        ```
        git push --mirror new-origin
        ```

***

### 6. 多人协作常见工作流程 <a href="#id-6.-duo-ren-xie-zuo-chang-jian-gong-zuo-liu-cheng" id="id-6.-duo-ren-xie-zuo-chang-jian-gong-zuo-liu-cheng"></a>

* **推送与拉取**
  1. 开发者在本地提交后，使用 `git push origin <branch-name>` 推送到远程分支。
  2. 若推送失败（通常因远程分支有更新），先执行 `git pull origin <branch-name>` 将远程更改合并到本地，解决冲突后再推送。
  3.  如果提示“no tracking information”，可用命令设置跟踪分支：

      Copy

      ```
      git branch --set-upstream-to=origin/<branch-name>
      ```
* **处理冲突**
  * 当合并或拉取过程中出现冲突时，手动编辑冲突文件，标记解决后再执行 `git add` 和 `git commit` 完成合并。

***

### QS <a href="#git" id="git"></a>

**Git reset/revert:**

git reset 用于将当前分支的HEAD指针和索引重置到指定的提交。它有三种模式：--soft（仅重置HEAD）、--mixed（重置HEAD和索引）和--hard（重置HEAD、索引和工作目录）。git revert 用于创建一个新的提交，撤销指定的历史提交，而不会改变提交历史。

**Merge/rebase:**

git merge 用于将另一个分支的更改合并到当前分支，生成一个新的合并提交。git rebase 用于将当前分支的更改应用到另一个分支的顶端，从而创建一个线性的提交历史。

**Pull/fetch:**

git pull 是 git fetch 和 git merge 的组合命令，它从远程仓库获取更新并合并到当前分支。git fetch 则仅从远程仓库获取更新，但不进行自动合并。

**git迁移项目到其他代码仓库，且保留分支与提交记录:**

指使用 Git 的工具和命令将一个项目从一个代码仓库迁移到另一个仓库，并保留所有的分支和提交历史记录。这通常涉及使用 git clone --mirror 和 git push --mirror 命令。

### Git reset

Git reset 是一个非常强大的命令，用于在 Git 历史中“回退”到某个指定的提交。它主要用于修改当前分支的指针（HEAD）、暂存区（index）以及工作目录（working directory）的状态，从而实现撤销提交、清理暂存区或者丢弃工作区改动。下面详细介绍 git reset 的工作原理、不同模式及其使用场景：

***

#### 1. 基本概念 <a href="#id-1.-ji-ben-gai-nian" id="id-1.-ji-ben-gai-nian"></a>

* **HEAD 指针：** Git 中的 HEAD 指针始终指向当前检出的提交。使用 git reset，可以将 HEAD 移动到任意指定的提交上。
* **暂存区与工作目录：** Git reset 同时可以修改暂存区和工作目录的内容，具体行为由所选模式决定。

***

#### 2. 三种主要模式 <a href="#id-2.-san-zhong-zhu-yao-mo-shi" id="id-2.-san-zhong-zhu-yao-mo-shi"></a>

**2.1 --soft 模式**

* **作用：**
  * 只移动 HEAD 指针，不改变暂存区和工作目录。
  * 相当于“撤销提交”，但保留了所有改动（这些改动仍在暂存区中）。
* **使用场景：**
  * 当你想要重新组织提交（例如合并几个提交）时，可以先用 --soft 回退到某个提交，再进行重新提交。
*   **示例：**

    Copy

    ```
    git reset --soft HEAD~1
    ```

    这会将 HEAD 回退到上一个提交，而本次提交的改动仍保留在暂存区中。

**2.2 --mixed 模式（默认模式）**

* **作用：**
  * 移动 HEAD 指针，并将暂存区重置为与指定提交一致，但不修改工作目录。
  * 所有在暂存区中的改动将变为未暂存状态（即出现在工作目录中）。
* **使用场景：**
  * 当你不希望暂存区中的改动保持记录，需要重新选择哪些改动纳入下次提交时，可以使用默认的 mixed 模式。
*   **示例：**

    Copy

    ```
    git reset HEAD~1
    ```

    或者显式指定 mixed：

    Copy

    ```
    git reset --mixed HEAD~1
    ```

    这将回退到上一个提交，暂存区的内容将更新为回退后的状态，而工作目录中的改动依然存在，但处于未暂存状态。

**2.3 --hard 模式**

* **作用：**
  * 同时重置 HEAD 指针、暂存区和工作目录。
  * 这意味着所有自指定提交之后的改动都会被丢弃（包括工作目录中的未提交改动）。
* **使用场景：**
  * 当你确定要完全放弃最近的改动，回到某个稳定状态时使用。
* **警告：**
  * 由于 --hard 模式会丢弃改动，该操作不可逆，请务必在执行前确认你不需要保留任何当前改动。
*   **示例：**

    Copy

    ```
    git reset --hard HEAD~1
    ```

    这将使当前分支回退到上一个提交，所有后续的改动都会被彻底删除。

***

#### 3. 附加说明 <a href="#id-3.-fu-jia-shuo-ming" id="id-3.-fu-jia-shuo-ming"></a>

* **回退与撤销：**
  * Git reset 用于修改本地提交历史。如果已经将提交推送到远程仓库，则直接使用 reset 可能会导致团队协作中的问题；此时，建议使用 git revert 来生成新的“撤销”提交，而不是修改公共历史。
* **慎用 --hard：**
  * \--hard 操作会覆盖工作目录中的所有改动，可能导致数据丢失。执行前最好用 git status 检查当前状态，并确保你不再需要未提交的内容。
* **与其他命令的关系：**
  * 除了 reset 外，git checkout 和 git revert 也可用于撤销改动，但它们的作用和适用场景不同。
  * git checkout 通常用于切换分支或恢复工作目录中文件的状态；而 git revert 用于在公共历史上撤销一个或多个提交。

***

#### 4. 使用示例场景 <a href="#id-4.-shi-yong-shi-li-chang-jing" id="id-4.-shi-yong-shi-li-chang-jing"></a>

1.  **撤销最近一次提交，但保留改动供修改后重新提交：**

    Copy

    ```
    git reset --soft HEAD~1
    ```

    这样，你可以对改动进行调整后再次 commit。
2.  **撤销暂存区的误操作，将改动从暂存区移回工作目录：**

    Copy

    ```
    git reset HEAD <file>
    ```

    这会将指定文件从暂存区中移除，但保留在工作目录中，方便你继续修改或选择性提交。
3.  **彻底放弃所有最近的改动：**

    Copy

    ```
    git reset --hard HEAD~1
    ```

    使用这种方式后，所有未提交的更改都将被删除，工作目录将回到上一个提交的状态。

***

总之，git reset 是一个非常灵活且强大的工具，能够帮助你在本地对提交历史、暂存区和工作目录进行精准控制。但同时，由于它可能会导致改动永久丢失，因此在使用时必须小心谨慎。 参考 Git 官方文档（请访问：https://git-scm.com/docs/git-reset）了解更详细的信息。

### 参考资料 <a href="#can-kao-zi-liao" id="can-kao-zi-liao"></a>

* [CS-Notes/Git.md · CyC2018/CS-Notes (GitHub)](https://github.com/CyC2018/CS-Notes/blob/master/git/Git.md)
* [Rogerdudler Git Guide (中文)](http://rogerdudler.github.io/git-guide/index.zh.html)
* [Git Flight Rules (中文)](https://github.com/k88hudson/git-flight-rules/blob/master/README_zh-CN.md)
* [Git Cherry-Pick 命令指南](https://deepinout.com/git/git-tutorial/19_git_cherry-pick_command__a_comprehensive_guide_to_its_usage.html)
* [Git Reset 介绍](https://initialcommit.com/blog/git-reset)
* [Merge vs Rebase 解析](https://blog.csdn.net/u014600626/article/details/108690049)
