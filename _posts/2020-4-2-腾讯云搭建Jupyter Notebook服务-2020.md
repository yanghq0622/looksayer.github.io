---
layout:     post                    # 使用的布局
title:      腾讯云搭建Jupyter Notebook服务               # 标题 
subtitle:   折腾几次，终于搞定，记录一下以免忘记 #副标题
date:       2020-04-02              # 时间
author:     YHQ	                     # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 服务器
    - python
    - Jupyter Notebook
---

> Jupyter Notebook是一个交互式笔记本，支持运行 40 多种编程语言，本质是一个 Web 应用程序，便于创建和共享文学化程序文档，支持实时代码，数学方程，可视化和 markdown。  
> 用途包括：数据清理和转换，数值模拟，统计建模，机器学习等等。

## 安装Jupyter Notebook
- **为 Jupyter Notebook 新建一个普通用户**  
之前图方便在 root 下安装的 Jupyter Notebook，发现居然可以用 `！rm -rf` 命令，所以最好还是新建一个账号专门运行相关服务。

- **安装安装 virtualenv 虚拟环境**  
从新用户登入服务器，安装 virtualenv 环境，为 Jupyter 创建一个独立的虚拟环境，与系统自带的 Python 隔离开来，指定的 Python 版本为 3。
```
pip install virtualenv #可以修改为国内的源，
virtualenv venv -p python3  # venv为自定义的虚拟环境名称，新建的 python 环境被放到当前目录下的 venv 目录中
```
- **激活新环境：**  
```
source venv/bin/activate‘ #命令提示符会有一个 venv 前缀，表示当前环境是一个名为 venv 的 Python 环境
```
- **在虚拟环境中安装 Jupyter**  
```
sudo pip install -U jupyter  #由于会安装其他相关的依赖库，这一步所需的时间可能较长。
```
## 配置Jupyter Notebook
- **建立项目目录**  
我们先为 Jupyter 相关文件准备一个目录：
```
mkdir ~/jupyter
cd jupyter``
mkdir root
```
- **准备密码密文**  
由于我们将以需要密码验证的模式启动 Jupyter，所以我们要预先生成所需的密码对应的密文。  
使用下面的命令，创建一个密文的密码：
```
from notebook.auth import passwd
passwd() #此时会让你两次输入密码，然后就会生成秘钥
执行后需要输入并确认密码，然后程序会返回一个 'sha1:...' 的密文
``` 
- **修改配置**  
我们使用 --generate-config 来参数生成默认配置文件：
```
jupyter notebook --generate-config --allow-root # 生成的配置文件在 ~/.jupyter/ 目录下
vim ~/.jupyter/jupyter_notebook_config.py  
```
然后在配置文件最下方加入以下配置：  
```
c.NotebookApp.ip = '*'
c.NotebookApp.allow_root = True
c.NotebookApp.open_browser = False
c.NotebookApp.port = 8888
c.NotebookApp.password = u'刚才生成的密文(sha:...)'
c.ContentsManager.root_dir = '/home/’用户名目录名‘/jupyter/root' # 工作目录
```
## 启动Jupyter Notebook
- **直接启动**  
使用以下指令启动 Jupyter Notebook：
```
jupyter notebook
```
此时，`访问 IP地址:8888` 即可进入 Jupyter 首页。
- **后台运行**
直接以 jupyter notebook 命令启动 Jupyter 的方式在连接断开时将会中断，所以我们需要让 Jupyter 服务在后台常驻。
先按下 `Ctrl + C` 并输入 y 停止 Jupyter 服务，然后执行以下命令：
```
nohup jupyter notebook > /home/’用户名目录名‘/jupyter/jupyter.log 2>&1 &
```
该命令将使得 Jupyter 在后台运行，并将日志写在 **/data/jupyter/jupyter.log** 文件中。


