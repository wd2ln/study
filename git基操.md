### 重要 指令 : 工作 流程 图 上 的 7 个 指令 (clone+fetch+pull+checkout+add+commit+push) 和 2 个 常用 指令 (log+merge)

### 常用 指令 速 查 :

- #### 基本 操作 类 :

  ```git
  初始化仓库
  git init
  查看日志这个命令很重要 ! ! 
  git log --pretty=oneline --all --graph --abbrev
  添加 到 和 暂 存 区
  git add < 文件 名 |.>
  提交到仓库
  git commit -m 注释 '
  合并指定分支到当前活跃分支
  git merge < 分 支 名 >
  ```

- #### 分 支 切 换 类 :

  ```
  切换到某个分支
  git checkout < 分 支 名 >
  创建 并 切换到某个分支(分支原来不存在 )
  git checkout -b < 分 支 名 >
  ```

- #### 远程 操作

  ```
  clone 远程仓库到本地
  git clone < 远程 地 址 > [本 地 文件 夹 ]
  拉取远端仓库的修改并合并
  git pull
  推送本地修改 到 远 端 分 支
  git push [--set-upstream] origin 分支名
  --set-upstream 表 示 和 远 端 分 支 绑 定 关联 关系 ， 只 有 第 一 次 推送 时 才 需要 此 参数
  ```

  