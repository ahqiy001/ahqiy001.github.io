# 初次上传本地项目到github的示例教程

# 说明

- 本文主要参考【[CSDN文章](https://blog.csdn.net/nanzhou520/article/details/135796134)】修改而来，主要是方便自己使用。
- 适用于上传本地已有的项目到github上
- 适用于云端新项目（仓库）
- github的访问可能需要加速，自行下载watt toolkit（原Steam++）搞下。

# 本地创建项目

创建一个新项目，得到以下项目文件。

* 这里以由hbuiderx构建的uniapp项目为例，带uni-ui组件。
* 如已有项目，使用已有项目即可。

![PixPin_2025-01-30_14-00-11.png](https://cdn.sa.net/2025/01/30/uni2EDlXWHZow9F.png)​

# 检查SSH KEY

在自己电脑上的`C:\Users\自己的电脑用户名\.ssh`目录下，看是否有以下密钥文件。

- 如果也没有.ssh文件夹，即说明没有相关密钥。
- 电脑用户名以C盘使用的这个为准，后期登录微软账号的也可能是使用原来的本地用户名。

![PixPin_2025-01-30_14-24-12.png](https://cdn.sa.net/2025/01/30/qcLsGdlJ7OXjSfY.png)​

检查是否有 id_rsa 和 id_rsa.pub 这两个文件，若有则直接进入下一步，否则执行以下命令

- 将以下命令中的邮箱换成自己github账号的邮箱（看情况换）。

```Vue
ssh-keygen -t rsa -C "email@example.com"
```

如下图所示，这里以演示邮箱为例。

![PixPin_2025-01-30_14-37-41.png](https://cdn.sa.net/2025/01/30/r46Ll8WYJnMIoN9.png)​

然后回到github网站，进入设置，左侧选择【SSH and GPG keys】，然后点击【New SSH key】创建一个新的SSH连接，【Title】标题随便取个英文的。

![PixPin_2025-01-30_14-42-44.png](https://cdn.sa.net/2025/01/30/4CIV8OkAhcSz1vF.png)​

# github上创建新项目（仓库）

在github上创建一个新的仓库，这里以一个私有仓库（项目）为例。

![PixPin_2025-01-30_14-03-21.png](https://cdn.sa.net/2025/01/30/BFztNmg81A2fL4i.png)​

然后会显示以下页面，包含一些初始化使用的命令。

项目没上传前，建议截图保存一下或者将命令复制到文本编辑器中备用。

![PixPin_2025-01-30_14-04-53.png](https://cdn.sa.net/2025/01/30/lvx2fpteMgECPqs.png)​

# [git工具](https://gitforwindows.org/)

下载安装一个【[git工具](https://gitforwindows.org/)】，已有的直接使用即可。

然后在项目目录内，空白处右键，选择【open git bash here】,打开git命令终端。

![PixPin_2025-01-30_14-10-35.png](https://cdn.sa.net/2025/01/30/9L8cR4wKTqO6JMj.png)​

![PixPin_2025-01-30_14-12-14.png](https://cdn.sa.net/2025/01/30/pztV45vUPnjRmQf.png)​

使用

```Vue
git init
```

命令，将项目目录变成 Git 可管理的仓库。

![PixPin_2025-01-30_14-16-42.png](https://cdn.sa.net/2025/01/30/XjECQgYkvO4bu7H.png)​

# 上传本地项目文件到云端

- 备用命令：移除项目关联。（仓库关联错误时使用）

  ```Vue
  git remote rm origin
  ```

第一次上传可能需要进行登录，在弹出的窗口里，选择浏览器登录。登录后，重新执行命令即可。（这里忘记截图了）

## 上传测试md文件

- **现在，开始执行git页面中的命令。**
- （ls）确保终端里的路径为项目路径。
- 下面是使用README.md测试，即页面上显示的命令。
- git init已经执行过了，执行后面的即可，建议一条一条的来。
- 这里的main是git项目的主分支。现在新创建的仓库好像都是main。有些可能是master。

```Vue
// git init（项目上传的第一次需要，后续上传不用）
echo "# git-test001x" >> README.md
//生成一个md测试文件
git add README.md
//选择上传文件（提交文件到暂存区，还没上传）
git commit -m "first commit"
//提交的信息（备注）
git branch -M main
git remote add origin https://github.com/ahqiy001/git-test001x.git
//关联仓库
git push -u origin main
//推送至仓库（实际上传）
```

![PixPin_2025-01-30_15-17-26.png](https://cdn.sa.net/2025/01/30/zcmDFdOWh2YQKkL.png)​

## 上传完整项目文件到云端

回到git终端，使用以下命令进行推送：选择目录—提交的信息提示—推送。

```Vue
git add . 
#添加当前目录下的所有文件到暂存区：
git commit -m "message" 
#提交暂存区文件到仓库，-m表示可以直接输入后面的message，内容随便输。
//git branch -M main （项目上传的第一次需要，后续上传不用）
//git remote add origin https://github.com/ahqiy001/git-test001x.git（项目上传的第一次需要，后续上传不用）
git push origin main 
或
git push -u origin main
```

![PixPin_2025-01-30_15-25-33.png](https://cdn.sa.net/2025/01/30/IHACDoqnKWf1Ebk.png)​

命令行上传完成后，刷新github页面后，即可看到上传的项目文件。

也可以将代码下载下来，查看是否与原来的一致。

# 遇到的问题

记录一下本次中遇到的问题和解决方法，这里是使用网上的方法解决的。

## 问题一：git Failed to connect to 127.0.0.1 port xxxx: Connection refused

方法来源：【[CSDN](https://blog.csdn.net/XH_jing/article/details/115095225)】

### 问题描述

在使用 git 拉取、提交代码的时候，会出现 `git Failed to connect to 127.0.0.1 port xxxx: Connection refused` 的问题。

原因：无法连接到 127.0.0.1: xxx 端口: 连接被拒绝。

### 解决方案

通过各种排查实验，总结出以下几种解决方案：

（补充：包括显示本地127.0.0.1: xxx，也给它取消下，正常情况是什么都不显示）

##### 方案一

思路：查询当前是否有代理，如果有就取消。

```Vue
// 首先，查一下当前全局的 http 代理：
git config --global http.proxy
// 如果有代理，就取消
git config --global --unset http.proxy


// 再查 https 的代理：
git config --global https.proxy
// 同样的，有就取消
git config --global --unset https.proxy
```

##### 方案二

上面的方案如果不行的话，再参考这个方案

```Vue
// 首先，查一下代理：
env|grep -i proxy
// 有就取消
unset http_proxy
unset https_proxy

// 再查
env|grep -i proxy
// 正常情况下是没有代理了
// 再次查询一下，如果还有的再取消
```

##### 方案三

修改环境变量

在系统变量中找到了变量 `http_proxy` 和 `https_proxy`，用户变量也可以看看有没有，删除他就可以了。

重启计算机。

再用 git，正常了，再查 `env|grep -i proxy` ，代理没有了。

代理没有了，就可以正常拉取、提交代码了。

## 问题二：error: src refspec master does not match any. error: failed to push some refs to

解决方法来源：【[CSDN](https://blog.csdn.net/wenb1bai/article/details/89363711)】

**解决git push 错误error: src refspec master does not match any. error: failed to push some refs to**

在和远程仓库关联后，通过 push 命令将本地仓库的文件推送到线上仓库时，

出现了如下错误

![https://i-blog.csdnimg.cn/blog_migrate/0765fbb4a6d93091fa7e3dd126041bba.png](https://cdn.sa.net/2025/01/30/em8iLXR3512wQIP.png)​

`error: src refspec master does not match any.`

`error: failed to push some refs to`

常见原因：

1. 本地 git 仓库目录下为空
2. 本地仓库 add 后未commit
3.git init 错误

用命令 git add + 文件名，把文件添加到仓库就行 ，然后正常 push 就好。

## 问题三：git push -u origin main Everything up-to-date branch 'main' set up to track 'origin/main'.

解决方法来源：【[腾讯云社区](https://cloud.tencent.com/developer/article/1896229)】

**git push 提交出现 Everything up-to-date 提示问题 - 腾讯云开发者社区 - 腾讯云以前通过 git 提交代码到 GitHub 上的个人 main 分支时，曾出现过这样一个很低级的错误——**

以前通过 git 提交代码到 GitHub 上的个人 main 分支时，曾出现过这样一个很低级的错误——

![https://ask.qcloudimg.com/http-save/yehe-6268234/afed73dcfc18e349d0b153d2646b3e22.png](https://cdn.sa.net/2025/01/30/QsKNJxOzLtnlo5F.png)​

出现这个错误原因，其实就是没有正确执行指令造成的，也就是没有正常提交数据。

一般按照以下命令提交，基本就没什么问题了——

```Vue
git add . #添加当前目录下的所有文件到暂存区：
git commit -m "message" #提交暂存区文件到仓库，-m表示可以直接输入后面的message
git push origin main 
```

**git push** 命用于从将本地的分支版本上传到远程并合并。

命令格式如下：

```Vue
git push <远程主机名> <本地分支名>:<远程分支名>
```

如果本地分支名与远程分支名相同，则可以省略冒号：

# 结束