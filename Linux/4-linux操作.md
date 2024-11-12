# linux操作相关
## 1 linux使用技巧
1、`ctrl + c`：快捷键，强制停止

![2024-11-12-22-26-46.png](./images/2024-11-12-22-26-46.png)

![2024-11-12-22-27-57.png](./images/2024-11-12-22-27-57.png)


2、`ctrl + d`：快捷键，退出或登出

3、`history`：查看历史命令

![2024-11-12-22-29-28.png](./images/2024-11-12-22-29-28.png)

4、`!+命令前缀`：自动执行上一次匹配前缀的命令

![2024-11-12-22-31-11.png](./images/2024-11-12-22-31-11.png)

5、`ctrl+r`：输入内容去匹配历史命令

![2024-11-12-22-35-16.png](./images/2024-11-12-22-35-16.png)

- 回车可以直接执行
- 键盘左右键可以得到这个命令，不执行

6、光标移动快捷键
- ctrl+a：跳到命令开头
- ctrl+e：跳到命令结尾
- ctrl+键盘左键：向左跳一个单词
- ctrl+键盘右键：向右跳一个单词

7、清屏
- 快捷键：ctrl+l
- 命令：clear

## 2 软件安装
操作系统安装软件一般有两种：
- 下载安装包自行安装
    - 比如win系统用exe、msi文件等，mac系统用dmg、pkg文件等
- 系统的应用商店内安装
    - 比如win系统有Microsoft store商店，mac系统有appstore商店
### 2.1 CentOS系统的yum
yum：.rpm包软件管理器，用于自动化安装配置linux软件，并可以自动解决依赖问题。

`yum [-y] [install | remove | search] 软件名称`

- -y：自动确认，无需手动确认安装或卸载过程

yum命令需要root权限，可以su切换用户或属于sudo。

<hr>
![2024-11-12-22-52-39.png](./images/2024-11-12-22-52-39.png)
有报错，是镜像源的问题。
解决：https://www.cnblogs.com/abel-he/p/18528680

![2024-11-12-22-55-07.png](./images/2024-11-12-22-55-07.png)

<hr>

![2024-11-12-22-55-53.png](./images/2024-11-12-22-55-53.png)


### 2.2 Ubuntu系统的apt
apt：.deb包软件管理器。
`apt [-y] [install | remove | search] 软件名称`

用法和yum一样，同样需要root权限。

![2024-11-12-23-04-45.png](./images/2024-11-12-23-04-45.png)


## 3 systemctl 控制软件的启动和关闭
linux系统很多软件均支持systemctl命令控制：启动、停止、开机自启，能够被systemctl管理的软件一般称为“服务”。

`systemctl start | stop | status | enable | disable 服务名`

- start：启动
- stop：关闭
- status：查看状态
- enable：开机自启
- disable：关闭开机自启

系统常见内置服务：
- NetworkManager，主网络服务
- Network，副网络服务
- firewalld，防火墙服务
- sshd，ssh服务（FinalShell远程登录linux使用的就是这个服务）

![2024-11-12-23-11-14.png](./images/2024-11-12-23-11-14.png)

如果第三方安装的软件没有自动集成到systemctl中，我们可以自己手动添加。

## 4 软连接