# git

Git是一个分布式的版本控制软件。

## Window git操作

* git init : 初始化git仓库，会生成一个.git文件，里面存储的就是文件夹的版本信息等数据。
* git status：检测文件夹的状态，三种状态的变化：
  * 红色：新增的文件或者修改了原来的老文件，执行git add filename｜.
  * 绿色：git已经管理起来的，执行git commit -m 'comment'
  * 没有颜色：已经commit的文件，生成了对应的版本。
* git add filename：没有add的文件是红色的，add之后的事绿色的，commit之后的文件没有颜色。
*  git rm --cached \<filename> :
* git commit -m 'comment' ，第一次commit可能不成功，因为如果是第一次安装git的话，需要配置用户名和邮箱，需要告诉git是谁进行的版本提交。
* git log：查看commit日志

## git三大区域

![image-20220603225403106](/Users/bytedance/Library/Application Support/typora-user-images/image-20220603225403106.png)

##  git回滚

回滚到之前的版本：

* 使用git log查看历史版本的版本号。
* 使用git reset --head \<版本号> 会滚到某个版本。

或者回滚到之后的版本：

* 使用git reflog 查看历史操作，可以看到被回滚的版本，这样回滚以后可以返回。
* 使用git reset --head \<版本号> 可以回滚到历史版本。