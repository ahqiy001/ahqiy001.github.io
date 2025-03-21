# FastApi(python)后端项目部署记录

# 说明
* 时间：20250321-2137

* 后端部署方式：打包成docker镜像

* 本地打包，云端部署。

* 云端如有相似环境，可直接在云端进行打包创建docker容器。

  ‍

* 含数据库配置。（这里使用本地已有数据进行）

* 一些基础配置和命令，这里不细讲，自己遇到则网上搜搜。

* 比如Linux命令、vim命令等。

  ‍

* 云服务器面板使用1panel面板进行管理。

* 如果使用的是宝塔面板，本笔记就只能作为参考了。

* 两个的主要部署环境不太同，宝塔主要使用Linux服务器真实环境，1panel主要使用docker容器环境。

* 也可以跳转笔记【[[息流笔记](https://flowus.cn/ahqi/share/2a0b20c5-b9e1-4324-88f7-461e23f63f6e?code=X2MT7Q)](https://flowus.cn/ahqi/share/2a0b20c5-b9e1-4324-88f7-461e23f63f6e?code=X2MT7Q)】进行查看宝塔面板部署过程（2024-1215创建）。

* 如果需要进行项目隔离，使用1panel好点。

‍

# 打包为docker镜像

‍

## 导出项目依赖

如果每个项目有对应的虚拟虚拟环境，那么使用pycharm的终端里，在当前项目下，直接实现使用命令：pip freeze \> requirements.txt #pip命令生成依赖性清单

```HTML
pip freeze > requirements.txt
```

后续安装该项目的依赖时，即可直接使用（**预留，这里不用**）：

```HTML
pip install -r requirements.txt
```

‍

‍

## 创建Dockerfile

接下来，需要创建一个Dockerfile文件（**没有后缀名**），用于定义Docker镜像的构建过程。在项目的根目录下创建一个名为Dockerfile的文件，并添加以下内容：

```python
# 使用官方 Python 3.12.8 镜像作为基础镜像
# 根据自己构建项目时选的版本来
FROM python:3.12-slim

# 设置版本信息
LABEL version="1.0.0"

# 设置工作目录
WORKDIR /app

# 安装系统依赖
RUN apt-get update && apt-get install -y \
    build-essential \
    libssl-dev \
    libffi-dev \
    python3-dev \
    cargo \
    && rm -rf /var/lib/apt/lists/*

# 更新 pip
RUN pip install --upgrade pip

# 复制依赖文件并安装依赖
COPY requirements.txt /app/
RUN pip install --no-cache-dir -r requirements.txt

# 复制项目文件到容器中
COPY . /app

# 暴露应用端口（FastAPI 默认使用 8000 端口）
EXPOSE 8085
# 与云端需要设置的端口相同

# 启动应用
CMD ["uvicorn", "app:apps", "--host", "0.0.0.0", "--port", "8086"]
```

## 清理不必要的文件

删除原始项目的虚拟环境和运行中产生的文件。

![PixPin_2025-03-20_21-48-59](https://cdn.sa.net/2025/03/21/keBFOlKWa1sMbCX.png)​

只保留使用到的代码，其他全部删除

主要后面是要复制到Linux环境里进行打包，所以删除一些不必要的文件，能加快打包速度。

![PixPin_2025-03-20_21-50-03](https://cdn.sa.net/2025/03/21/tESBFmh8DiqAn3Z.png)​

‍

## 打开Linux系统

这里使用虚拟机系统来进行本地打包操作。

版本：Ubuntu2404

系统需要自行准备好，这里仅测试打包流程。

也可以使用其他Linux系统，或者直接将代码上传云端，在云端打包。

以下为Ubuntu图形化界面本地打包流程。

![PixPin_2025-03-20_21-58-46](https://cdn.sa.net/2025/03/21/KpC7BJMswQbAE1G.png)​

输入用户密码进入系统，然后需要使用终端进行操作。

![PixPin_2025-03-20_22-00-22](https://cdn.sa.net/2025/03/21/4EphUZ9FH5uPogS.png)​

## 安装docker

这里的Linux系统还没有安装docker，所以需要进行安装

>  <span data-type="text" style="color: var(--b3-font-color1);">如果已经安装的，就跳过这一步。</span>

使用以下命令进行安装

```python
sudo apt-get update
sudo apt-get install -y docker.io
```

输入密码，建议使用左侧键盘上的数字键来输入数字。

右侧数字小键盘可能没有开启，会提示错误。

![PixPin_2025-03-20_22-04-51](https://cdn.sa.net/2025/03/21/dJrbFmsoHxzGI1U.png)​

如下图，安装完成

![PixPin_2025-03-20_22-07-49](https://cdn.sa.net/2025/03/21/dkaJEj84yuAbeSF.png)​

‍

最好用命令也测试一下是否安装成功

```python
docker --version
```

![PixPin_2025-03-20_22-36-42](https://cdn.sa.net/2025/03/21/Yg39hJKM5cln8or.png)

如显示docker版本，即为成功。

‍

## 安装python环境

这个是必要的，没有的也需要安装下。

最好和你的项目版本相近

```python
sudo apt install python3.12
```

如果提示进程锁，则重启下Linux系统。

![PixPin_2025-03-20_22-44-21](https://cdn.sa.net/2025/03/21/F4ZN9JVYkDpXxUS.png)​

‍

进程走完，检查是否安装成功

```python
python3.12 --version
```

![PixPin_2025-03-20_22-46-22](https://cdn.sa.net/2025/03/21/ZzfSpQYUqkx2Fwg.png)

显示python版本号，则说明python安装成功。

‍

## 安装pip

有些没有自带pip，需要手动安装下。

```python
sudo apt install python3-pip
```

安装完成后，测试一下

```python
pip list
```

![PixPin_2025-03-20_22-54-00](https://cdn.sa.net/2025/03/21/2s1yQzophDISaFk.png)​

如果显示一些软件包，则说明pip安装成功。

‍

## 复制文件到Linux系统

将项目压缩为tar格式的压缩包，方便复制到Linux系统里。

文件名字尽量短点，方便查看目录路径

![PixPin_2025-03-20_22-22-07](https://cdn.sa.net/2025/03/21/faSVGghzFExt8Jm.png)​

然后把压缩包后缀删了，再复制到Linux系统里，复制成功后在添加后缀。

（我这里是需要这样才能复制，好像是虚拟机连接有问题。如果正常复制可以成功就不用改。）

![PixPin_2025-03-20_22-23-34](https://cdn.sa.net/2025/03/21/wPeO1r5TVfC2G6j.png)​

gif演示：

![PixPin_2025-03-20_22-26-12](https://cdn.sa.net/2025/03/21/Zim6Uew3DbJLvSC.gif)​

解压项目代码：直接选中文件，然后右键提取。

如习惯命令，也可在终端使用命令进行解压操作。

![PixPin_2025-03-20_22-27-14](https://cdn.sa.net/2025/03/21/6hNVXr81LF2RGiM.png)​

然后进入项目目录内，右键打开终端。

这里名字还是太长了，建议短点。

![PixPin_2025-03-20_22-28-19](https://cdn.sa.net/2025/03/21/BskQmNIZntcg3Ch.png)

核对项目路径。

```python
pwd
```

![PixPin_2025-03-20_22-29-19](https://cdn.sa.net/2025/03/21/Bh7dz2mtlunQA6C.png)​

‍

‍

## 构建docker镜像

### 打包（失败）

OK，准备好docker环境、项目文件和Dockerfile文件后，可以开始打包了。

回到项目目录内，然后打开终端使用以下格式的命令进行打包。

```python
docker build -t 项目名称（英文）:1.0.0（项目版本） 空格.
```

‍

本项目的命令：

```python
sudo docker build -t fastapi_cw_test:1.0.0 .
```

如果报错，则添加root权限，再进行测试。

![PixPin_2025-03-20_22-58-42](https://cdn.sa.net/2025/03/21/DxHl583SiChIqG1.png)​

```python
sudo docker build -t fastapi_cw_test:1.0.0 .
```

‍

#### 权限问题：

![PixPin_2025-03-20_22-59-57](https://cdn.sa.net/2025/03/21/lDmhFksgqpKRQGx.png)

#### 网络问题：

这个是docker官方镜像源经常会出现无法连接的缘故，只能网上查找相关的可用源来替换。

![PixPin_2025-03-20_23-01-08](https://cdn.sa.net/2025/03/21/idVsUyIX53Fpuzv.png)

‍

‍

### 替换docker镜像源

新开个终端，然后切换到docker目录

```python
cd  /etc/docker
```

新建daemon.json

>  记得带sudo（权限）

```python
sudo touch daemeon.json
```

![PixPin_2025-03-20_23-07-11](https://cdn.sa.net/2025/03/21/s8P6Oylku7h1QGe.png)

安装vim（自带vi不好用）

```python
sudo apt install vim 
```

‍

编辑（带权限）

```python
sudo vim /etc/docker/daemon.json
```

替换为可用镜像源，需要网上寻找新的可用源。

```python
{
  "registry-mirrors": [
    "https://docker-0.unsee.tech",
    "https://docker-cf.registry.cyou",
    "https://docker.1panel.live"
  ]
}
```

按下图这种添加进去。

（以下源已失效）

![PixPin_2025-03-20_23-14-44](https://cdn.sa.net/2025/03/21/YrtSWIXBHK1jnZl.png)

vim命令补充：

加！为强制操作，退不出来时使用。

```python
：wq!强制保存并删除
：q!强制不保存并退出
```

‍

重启 Docker 服务：

```bash
sudo systemctl restart docker
```

‍

### 检查 Docker 服务状态

确保 Docker 服务正常运行，并且没有资源限制方面的问题。可以使用以下命令检查 Docker 服务状态：

‍

```bash
sudo systemctl status docker
```

如果服务没有正常运行，可以使用以下命令启动或重启 Docker 服务：

```bash
sudo systemctl start docker
# 或者
sudo systemctl restart docker
```

### 再次尝试

然后再次尝试拉取和构建：

需要切换目录到项目内，然后再右键运行终端（最好开两个终端，方便调试镜像源）

```bash
sudo docker build -t fastapi_cw_test:1.0.0 .
```

需要多试几下。

如下则为镜像源连接成功

![PixPin_2025-03-20_23-33-26](https://cdn.sa.net/2025/03/21/7LaUNtOYIXx3qSK.png)

‍

开始安装相关依赖

![PixPin_2025-03-20_23-34-08](https://cdn.sa.net/2025/03/21/HydACBlpS5OkmuV.png)

### 打包成功

晚上断电了，然后早上又改了下项目的目录名字，又重新运行了打包命令，以下是成功了。

![PixPin_2025-03-21_07-18-03](https://cdn.sa.net/2025/03/21/8LXSEAsKevVBw2x.png)​

现在构建好的镜像是保存在docker中的，需要将其打包成docker镜像文件并导出到Windows系统，方便上传到云服务器使用。

‍

‍

## 导出镜像到文件

如果想把镜像导出为一个文件，可以使用 docker save 命令：

```python
sudo docker save -o fastapi_cw_test_1.0.0.tar fastapi_cw_test:1.0.0
```

![PixPin_2025-03-21_07-31-52](https://cdn.sa.net/2025/03/21/4YJVH6FXCWkzmai.png)​

该命令会把 fastapi_cw_test:1.0.0 镜像保存为 fastapi_cw_test_1.0.0.tar 文件，在当前目录找到这个文件。

之后，若要在其他 Docker 环境中使用这个镜像，可使用 docker load 命令加载： **（预留，本笔记使用图形化部署，没用着）**

```python
docker load -i fastapi_cw_test_1.0.0.tar
```

‍

## 复制镜像到Windows系统

如果镜像文件太大，复制不出来，则可能是vm tools工具的缘故，需要重新安装下vm tools工具

### 安装vm tools工具

参考【[[链接](https://blog.csdn.net/qq_40259641/article/details/79022844)](https://blog.csdn.net/qq_40259641/article/details/79022844)】【[[链接2](https://www.cnblogs.com/xilimiss510/p/16120635.html#:~:text=%E5%9C%A8Vmware%E8%BD%AF%E4%BB%B6%E4%B8%ADUbuntu%E8%BF%90%E7%94%A8%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9%E5%8A%9F%E8%83%BD%E4%B8%BB%E8%A6%81%E5%81%9A%E4%B8%A4%E6%AD%A5%EF%BC%9A%20%E7%BB%99Vmware%E5%AE%89%E8%A3%85Vmware%20Tools%E3%80%82%20%E3%80%90%E5%AE%89%E8%A3%85%E5%A5%BDVmware%20Tools%E5%90%8E%E5%8F%AF%E4%BB%A5%E5%AE%9E%E7%8E%B0%E4%B8%BB%E6%9C%BA%E4%B8%8E%E8%99%9A%E6%8B%9F%E6%9C%BA%E4%B9%8B%E9%97%B4%E7%9A%84%E6%96%87%E4%BB%B6%E5%85%B1%E4%BA%AB%E5%92%8C%E5%A4%8D%E5%88%B6%E3%80%81%E7%B2%98%E8%B4%B4%E6%93%8D%E4%BD%9C%E3%80%91,%E5%9C%A8%E8%99%9A%E6%8B%9F%E6%9C%BAUbutnu%20%2Fmnt%2Fhgfs%E6%96%87%E4%BB%B6%E5%A4%B9%E4%B8%8B%E6%89%BE%E4%B8%8D%E5%88%B0%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9%E6%88%96%E8%80%85%E5%9C%A8Ubuntu%E7%BB%88%E7%AB%AF%E4%B8%AD%E8%BF%90%E8%A1%8C%E5%91%BD%E4%BB%A4%E8%A1%8C%20%EF%BC%9A%20vmware%20-hgfsclient%20%E6%9F%A5%E7%9C%8B%E5%BC%80%E5%90%AF%E7%9A%84%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9)](https://www.cnblogs.com/xilimiss510/p/16120635.html#:~:text=%E5%9C%A8Vmware%E8%BD%AF%E4%BB%B6%E4%B8%ADUbuntu%E8%BF%90%E7%94%A8%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9%E5%8A%9F%E8%83%BD%E4%B8%BB%E8%A6%81%E5%81%9A%E4%B8%A4%E6%AD%A5%EF%BC%9A%20%E7%BB%99Vmware%E5%AE%89%E8%A3%85Vmware%20Tools%E3%80%82%20%E3%80%90%E5%AE%89%E8%A3%85%E5%A5%BDVmware%20Tools%E5%90%8E%E5%8F%AF%E4%BB%A5%E5%AE%9E%E7%8E%B0%E4%B8%BB%E6%9C%BA%E4%B8%8E%E8%99%9A%E6%8B%9F%E6%9C%BA%E4%B9%8B%E9%97%B4%E7%9A%84%E6%96%87%E4%BB%B6%E5%85%B1%E4%BA%AB%E5%92%8C%E5%A4%8D%E5%88%B6%E3%80%81%E7%B2%98%E8%B4%B4%E6%93%8D%E4%BD%9C%E3%80%91,%E5%9C%A8%E8%99%9A%E6%8B%9F%E6%9C%BAUbutnu%20%2Fmnt%2Fhgfs%E6%96%87%E4%BB%B6%E5%A4%B9%E4%B8%8B%E6%89%BE%E4%B8%8D%E5%88%B0%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9%E6%88%96%E8%80%85%E5%9C%A8Ubuntu%E7%BB%88%E7%AB%AF%E4%B8%AD%E8%BF%90%E8%A1%8C%E5%91%BD%E4%BB%A4%E8%A1%8C%20%EF%BC%9A%20vmware%20-hgfsclient%20%E6%9F%A5%E7%9C%8B%E5%BC%80%E5%90%AF%E7%9A%84%E5%85%B1%E4%BA%AB%E6%96%87%E4%BB%B6%E5%A4%B9)】

如果安装后还是，无法复制出来，那就只能使用网页或者配置ssh工具连接来传了。

![PixPin_2025-03-21_07-57-13](https://cdn.sa.net/2025/03/21/r5873IqsKSEjXHz.png)​

我的情况就是无法复制，vm-tools工具也是安装失败。同时网页微信文件助手也无法使用。

只能切换为ssh工具再测试了

‍

### 安装 OpenSSH 服务

```python
sudo apt install ssh
```

![PixPin_2025-03-21_08-19-26](https://cdn.sa.net/2025/03/21/YxrC2Xs3OyFUnuq.png)

**启用 SSH 服务以在启动时启动**

```python
sudo systemctl enable ssh
```

‍

**验证 SSH 服务状态**

```python
sudo systemctl status ssh
```

![PixPin_2025-03-21_08-20-47](https://cdn.sa.net/2025/03/21/bHaK8PmAJ76zs9X.png)​

**配置防火墙**

如果使用的是 UFW 防火墙，则必须将其配置为允许 SSH 连接。此步骤对于远程访问至关重要，尤其是在受保护的网络环境中作时。

```python
sudo ufw allow ssh
sudo ufw enable
```

![PixPin_2025-03-21_08-21-40](https://cdn.sa.net/2025/03/21/S4HYQv7zwMKsF6E.png)

‍

这里用自己习惯的ssh工具进行即可。有个免费的叫finalshell。

使用ssh工具进行连接，然后下载镜像文件（即将本地Linux系统的文件导出到Windows系统）

![PixPin_2025-03-21_08-27-02](https://cdn.sa.net/2025/03/21/hbe8pP6XTwx7nYd.png)​

![PixPin_2025-03-21_08-25-36](https://cdn.sa.net/2025/03/21/w6geN8AE9ZlLSty.png)​

如下，说明已经将其成功导出到windows系统了。

‍

# 上传到云服务器

可以使用ssh工具，也可以直接在服务器面板进行。

这里使用ssh工具，在服务器选择一个目录，用于临时存放镜像文件。

![PixPin_2025-03-21_08-32-36](https://cdn.sa.net/2025/03/21/4NUPtpZFK1Jo8Ea.png)

![PixPin_2025-03-21_08-34-08](https://cdn.sa.net/2025/03/21/2mq6nNfelPRsjAV.png)

我使用的服务器面板是【[[1panel](https://1panel.cn/docs/installation/online_installation/)](https://1panel.cn/docs/installation/online_installation/)】面板。

后续的配置使用这个面板和ssh工具搭配进行。

>  如果使用的是宝塔面板，后续步骤和本笔记会有部分不同。主要是数据库部分。

**主要是区别是1panel基本都是基于docker容器化部署。宝塔的则主要为直接在Linux服务器环境中部署。** 

所以如果使用的是宝塔面板，那本笔记就仅做参考了。

或者跳转笔记【[[息流笔记](https://flowus.cn/ahqi/share/2a0b20c5-b9e1-4324-88f7-461e23f63f6e?code=X2MT7Q)](https://flowus.cn/ahqi/share/2a0b20c5-b9e1-4324-88f7-461e23f63f6e?code=X2MT7Q)】进行查看宝塔面板部署过程（2024-1215创建）。

![PixPin_2025-03-21_08-35-31](https://cdn.sa.net/2025/03/21/mw3OqM6cdreUPxb.png)

OK，镜像上传到云端服务器准备完成。

‍

# 创建项目容器

导入后端项目docker镜像到容器仓库中。

在1panel面板，点击左侧菜单的容器，然后选择上方的镜像->导入镜像->选择上传后的项目镜像文件。

![PixPin_2025-03-21_18-46-35](https://cdn.sa.net/2025/03/21/MauIJLodKj9zcw1.png)

然后创建对应的项目docker容器。

名字随便填，然后选择刚刚导入仓库的项目镜像

![PixPin_2025-03-21_19-00-16](https://cdn.sa.net/2025/03/21/rT3CIMlyVf4NFEq.png)​

确保容器正常运行

![PixPin_2025-03-21_19-03-11](https://cdn.sa.net/2025/03/21/kqGoCnIORtWmjAY.png)

然后在系统--防火墙中，开放对应的端口。

注意如果开放后依旧打不开，可能是云服务器厂商那边也需要打开下对应的端口。

>  阿里云端口开发参考：

![image](https://cdn.sa.net/2025/03/21/QmDZtBnHNVzpLb9.png)​

其他厂商的也类似，基本都在安全组里设置。

‍

打开fastapi自带的接口文档（云服务器公网地址）

![PixPin_2025-03-21_19-02-05](https://cdn.sa.net/2025/03/21/uNt6HDRFxMdbE2n.png)

OK，能打开就说明容器运行成功。

**至此，后端python项目docker容器化部署完成。** 

后续的则是数据库连接配置的内容。

‍

‍

# 接入数据库

## 导出本地数据库文件

使用可以导出数据库的软件工具，进行数据库文件的导出。

这里使用的是navicat for mysql软件进行，也可以使用自己习惯的软件。

进入软件，选择对应的数据库，然后导出（转储SQL文件）

![PixPin_2025-03-21_08-39-35](https://cdn.sa.net/2025/03/21/purL28oW7XRsz1d.png)​

找一个目录放着，方便等会上传到服务器。

![PixPin_2025-03-21_08-40-10](https://cdn.sa.net/2025/03/21/6ATtr2fIOJ3mj5n.png)​

如下即为导出成功。

![PixPin_2025-03-21_08-39-13](https://cdn.sa.net/2025/03/21/MEFhx2jpTA8swCn.png)​

‍

## 安装mysql服务

这里是已经创建过数据库容器了，没有的在应用商店中搜索，按页面提示安装就行。

>   **在1panel面板中，mysql数据库也是使用容器部署的，所以直接在应用商店中安装即可。**

![PixPin_2025-03-21_19-07-29](https://cdn.sa.net/2025/03/21/uN8s3hGmoQlpI6T.png)

创建一个专用用户来作为数据库连接配置使用。

![PixPin_2025-03-21_08-44-08](https://cdn.sa.net/2025/03/21/jWS8vYpfAxP2LrJ.png)​

‍

## 导入到云端数据库

然后将数据库文件导入到对应的数据库中。

正常情况在这里导入即可，如果不成功则切换其他软件来远程连接导入。

![PixPin_2025-03-21_18-22-21](https://cdn.sa.net/2025/03/21/Ju8hLCKmHezkylj.png)

‍

# 配置数据库连接

## 修改后端数据库连接配置代码

**1panel采用容器化部署，所以直接来容器这里查看数据库的内网地址。** 

查看mysql的内网地址：

![PixPin_2025-03-21_18-42-33](https://cdn.sa.net/2025/03/21/9twrOARzLI7su54.png)​

手动进入后端项目的容器内部目录

（没找到快捷入口，只能手动找了）

位置一般在

```python
/var/lib/docker/overlay2/***一堆字母数字***用时间排序来找目录/merged/app/项目内部目录
```

然后我这里找的是数据库连接配置文件。在下图位置，请根据自己的项目目录来找并进行配置。

![PixPin_2025-03-21_19-18-52](https://cdn.sa.net/2025/03/21/z8kqpEVRPYjIKiM.png)

修改为云端数据库的连接信息，地址为mysql的内网地址。

>  **然后保存，再重启后端容器，重启后端容器，重启后端容器！**

‍

‍

然后接口文档测试，能否获取到数据。

![PixPin_2025-03-21_19-26-22](https://cdn.sa.net/2025/03/21/q4DHaUr1nM5QOwc.png)​

这里可以获取到数据，说明数据库连接也配置成功了。

至此，后端项目docker化部署和接入数据库配置完成。

本笔记结束。

# 结束

‍