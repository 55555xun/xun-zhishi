# Git 常用指令

> 每行命令后的 `#` 为作用说明。

## 配置

```bash
git config --global user.name "你的名字"          # 设置全局用户名
git config --global user.email "你的邮箱"         # 设置全局邮箱
git config --list                                # 查看所有 Git 配置
```



## 创建 / 克隆仓库

```bash
git init                                         # 初始化本地 Git 仓库
git clone <仓库地址>                              # 克隆远程仓库到本地
```



## 查看状态 / 历史

```bash
git status                                       # 查看工作区和暂存区状态
git diff                                         # 查看未暂存的修改内容
git diff --staged                                # 查看已暂存的修改内容
git log --oneline --graph --all                  # 查看简洁提交历史图
git show <commit>                                # 查看某次提交的详细内容
```



## 提交代码

```bash
git add <file>                                   # 添加指定文件到暂存区
git add .                                        # 添加所有修改到暂存区
git add -p                                       # 交互式选择要提交的代码片段
git commit -m "提交说明"                          # 提交暂存区内容并写提交说明
git commit --amend                               # 修改最近一次提交
```



## 分支

```bash
git branch                                       # 查看本地分支
git branch -a                                    # 查看所有本地和远程分支
git switch <branch>                              # 切换到指定分支
git switch -c <branch>                           # 新建分支并切换过去
git checkout -b <branch>                         # 新建并切换分支，旧写法
git merge <branch>                               # 将指定分支合并到当前分支
git rebase <branch>                              # 将当前分支变基到指定分支
git branch -d <branch>                           # 删除已合并的本地分支
git branch -D <branch>                           # 强制删除本地分支
```



## 远程仓库

```bash
git remote -v                                    # 查看远程仓库地址
git remote add origin <url>                      # 添加名为 origin 的远程仓库
git remote set-url origin <url>                  # 修改 origin 的远程地址
git fetch                                        # 拉取远程更新但不合并
git pull                                         # 拉取远程更新并合并到当前分支
git push                                         # 推送当前分支到远程仓库
git push -u origin main                          # 首次推送 main 并设置上游分支
git push origin --delete <branch>                # 删除远程分支
```



## 撤销 / 回退

```bash
git restore <file>                               # 丢弃工作区指定文件的修改
git restore --staged <file>                      # 取消指定文件的暂存
git reset --soft HEAD~1                          # 撤销最近一次提交，保留暂存区和工作区
git reset --mixed HEAD~1                         # 撤销最近一次提交和暂存，保留工作区
git reset --hard HEAD~1                          # 彻底回退到上一次提交，丢弃修改
git revert <commit>                              # 生成一次反向提交，安全抵消指定提交
```



## 暂存工作区

```bash
git stash                                        # 暂存当前未提交的修改
git stash list                                   # 查看暂存列表
git stash pop                                    # 恢复最近暂存并删除该暂存记录
git stash apply                                  # 恢复最近暂存但保留暂存记录
git stash drop                                   # 删除最近一条暂存记录
```



## 标签

```bash
git tag                                          # 查看所有标签
git tag -a v1.0 -m "版本说明"                     # 创建附注标签 v1.0
git push origin --tags                           # 推送所有标签到远程
```



## 其他常用

```bash
git cherry-pick <commit>                         # 将指定提交应用到当前分支
git clean -fd                                    # 删除未跟踪的文件和目录
git fetch --prune                                # 清理远程已删除的分支引用
git rebase -i HEAD~3                             # 交互式整理最近 3 次提交
```



## 日常开发流程

```bash
git status                                       # 查看当前状态
git add .                                        # 添加所有修改到暂存区
git commit -m "feat: 完成某功能"                  # 提交并写说明
git pull                                         # 拉取远程更新并合并
git push                                         # 推送到远程仓库
```



## 危险操作提醒

```bash
git reset --hard                                 # 丢弃所有未提交修改，危险
git clean -fd                                    # 删除未跟踪文件和目录，危险
git push -f                                      # 强制推送，可能覆盖远程历史，危险
```







# 杂

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 172021.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 201257.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 202013.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 203501.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 203854.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 205219.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 211945.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 212350.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 213430.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 214035.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 214355.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 214519.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 214942.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 215558.png)

![](C:\Users\5\OneDrive\图片\Screenshots\屏幕截图 2026-04-19 220003.png)