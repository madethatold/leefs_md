## Linux下Anaconda使用

```shell
# 列出所有conda环境
conda env list 
# 激活/进入某一个环境
source activate 
conda activate lif1
# 创建一个虚拟环境
conda create -n lif1 python=3.8
# 关闭虚拟环境
conda deactivate
# 删除环境
conda remove -n lif1--all
# 查看该环境所有的库（不进入某一环境执行该指令默认为base的所有库）
conda list
# 为某个环境安装依赖
pip install matplot
pip install matplot=1.1
# 卸载某个环境下的某个依赖
pip uninstall matplot
# 使用镜像源下载依赖
pip install your_lib_name=x.x.x -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com --trusted-host your_lib_url_host
# 其他可用镜像源
-i http://pypi.douban.com/simple/ --trusted-host pypi.douban.com
-i https://pypi.tuna.tsinghua.edu.cn/simple/ --trusted-host pypi.tuna.tsinghua.edu.cn
-i http://pypi.mirrors.ustc.edu.cn/simple/ --trusted-host pypi.mirrors.ustc.edu.cn
# Conda 库镜像源配置
conda config --remove-key channels
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.ustc.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.bfsu.edu.cn/anaconda/cloud/pytorch/
conda config --set show_channel_urls yes
pip config set global.index-url https://mirrors.ustc.edu.cn/pypi/web/simple


```

## Linux GPU 环境配置（驱动、Cuda和Cudnn安装）

http://lanping.tech/archives/%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AElinuxgpu%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE%E9%A9%B1%E5%8A%A8cuda%E5%92%8Ccudnn%E5%AE%89%E8%A3%85

以下是Linux服务器配置Python+PyTorch+CUDA深度学习环境

[Linux服务器配置Python+PyTorch+CUDA深度学习环境_NSJim的博客-CSDN博客_linux pytorch cuda](https://blog.csdn.net/NSJim/article/details/115386936)

## Pycharm远程连接服务器

[Pycharm远程连接服务器进行代码的运行与调试_夏小悠的博客-CSDN博客_pycharm远程连接服务器跑代码](https://blog.csdn.net/qq_42730750/article/details/119249193?spm=1001.2014.3001.5501)

## 虚拟机无法复制粘贴

```shell
sudo apt install open-vm-tools 
sudo apt install open-vm-tools-desktop
```



## linux重启网络无法使用

```shell
sudo service network-manager restart
```

## xshell连接不上的若干原因

1. 主机与服务器不在同一网段

   linux ifconfig win ipconfig查看ip

2. 防火墙未关闭

3. sshd未打开

4. 22号端口未打开

## 服务器中linux环境的路径

进入某个环境，查看路径

```shell
which python
```

## Python终端常用的pip命令

[pip常用命令_NSJim的博客-CSDN博客_pip命令](https://blog.csdn.net/NSJim/article/details/115386688)

## 远程连接Linux服务器 - Windows系统

[远程连接Linux服务器 - Windows系统_NSJim的博客-CSDN博客_远程连接linux服务器](https://blog.csdn.net/NSJim/article/details/115366651)

## Pycharm连接服务器运行代码无法显示图像

利用Pycharm远程连接服务器调用Matplotlib绘图时无法在本地显示图形

[解决Pycharm远程连接服务器调用Matplotlib绘图时无法在本地显示图形的问题 - DuanYongchun - 博客园 (cnblogs.com)](https://www.cnblogs.com/dyc99/p/12936080.html)

[ Pycharm 远程连接服务器无法显示matplotlib图像的问题_Yarn_的博客-CSDN博客](https://blog.csdn.net/Yarn_/article/details/86506591)























