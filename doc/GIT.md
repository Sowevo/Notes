# Git下载某一个commit提交的文件

```shell
# 先比较出来需要的文件   
# commit-id^ 表示commit的上一个版本
# 并把文件名写入sparse-checkout
$ git diff --name-only <commit-id>^ <commit-id> >.git/info/sparse-checkout
# 打开稀疏检出
$ git config core.sparseCheckout true
$ git checkout
```

# Git合并

```shell
# 确保来源分支代码最新
$ git checkout QC_online
$ git pull

# 确保目标分支代码最新
$ git checkout QC_1916
$ git pull

# 在目标分支上执行操作,禁止快进式合并
$ git merge --no-f origin/QC_online

# 如果有冲突
$ git merge --no-f origin/QC_online
Auto-merging index.html
CONFLICT (content): Merge conflict in bus365mngr/app/BP/system/BusCityInfoBP.java
Automatic merge failed; fix conflicts and then commit the result.
# 开始解决冲突
# 查看冲突状态
$ git status
# 开始处理
$ git mergetool

This message is displayed because 'merge.tool' is not configured.
See 'git mergetool --tool-help' or 'git help config' for more details.
'git mergetool' will now attempt to use one of the following tools:
opendiff kdiff3 tkdiff xxdiff meld tortoisemerge gvimdiff diffuse diffmerge ecmerge p4merge araxis bc codecompare emerge vimdiff
Merging:
bus365mngr/app/BP/system/BusCityInfoBP.java

Normal merge conflict for 'bus365mngr/app/BP/system/BusCityInfoBP.java':
  {local}: modified file
  {remote}: modified file
Hit return to start merge resolution tool (tortoisemerge):

# 输入回车用默认的合并工具
# 在工具中解决冲突

# 在次查看状态确认已解决
$ git status

# 完成合并提交
$ git commit

# 提交到远端
$ git push
```

# 使用Beyond Compare作为对比工具

MAC的话要先安装命令行工具

![MAC的话要先安装命令行工具](https://up.sowevo.com/img/20201203152302.png)

```ini
# Windows需要修改.gitconfig文件 加入如下配置
[diff]
	tool = bc4
[difftool "bc4"]
	cmd = \"D:/Beyond Compare  4.2.7.23425 x64 破解版/BCompare.exe\" \"$LOCAL\" \"$REMOTE\"
[merge]
	tool = bc4
[mergetool "bc4"]
	cmd = \"D:/Beyond Compare  4.2.7.23425 x64 破解版/BCompare.exe\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\"
	trustExitCode = true
	
# MAC user目录下的.gitconfig
[diff]
        tool = bcomp
[difftool "bcomp"]
        cmd = \"/usr/local/bin/bcomp\" \"$LOCAL\" \"$REMOTE\"
[difftool]
        prompt = false
[merge]
        tool = bcomp
[mergetool]
        prompt = false
[mergetool "bcomp"]
        cmd = \"/usr/local/bin/bcomp\" \"$LOCAL\" \"$REMOTE\" \"$BASE\" \"$MERGED\"
```

# 获取两个分支的差异文件

```shell
# 比较YD2007,online两个分支的差异文件的列表,记录到文件
$ git diff YD2007 QC_online --stat --stat-name-width=1000 --stat-graph-width=-10 > 123.txt
```

# 设置用户名密码

```shell
# 设置用户名密码
git config --global user.name sowevo
git config --global user.email i@sowevo.com
```

# 快速拉取最新代码

- 通过`git remote -v`查看是否有源头仓库的别名和地址。

  例如这里origin就是你自己的仓库，upstream是你fork的源头仓库。

  ```shell
  $ git remote -v
  origin  https://github.com/JunzhouLiu/cxmooc-tools.git (fetch)
  origin  https://github.com/JunzhouLiu/cxmooc-tools.git (push)
  upstream        https://github.com/CodFrm/cxmooc-tools.git (fetch)
  upstream        https://github.com/CodFrm/cxmooc-tools.git (push)
  ```

- fork仓库后，将你的仓库拉到本地，如果没有源头仓库，则添加源头仓库

  ```shell
  $ git remote add upstream https://github.com/JunzhouLiu/BILIBILI-HELPER.git
  ```

- 更新上游仓库main分支的代码（pull操作实际上是 `fetch+merge`）

  ```shell
  $ git pull upstream main
  ```

- 将从源头仓库更新后的代码推送到你自己的github仓库

  ```shell
  $ git push origin main 
  ```

- 这样你就能快速的从主仓库拉取最新的代码，并更新到你自己的仓库里了。

