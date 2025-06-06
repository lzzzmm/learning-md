# Linux权限
## 1 root用户
Linux系统中拥有最大权限的账户名是root（超级管理员）。

### 1.1 su 切换用户
`su [-] [用户名]`

- \- ：可选，表示是否在切换用户后加载环境变量，建议带上
- 用户名：表示要切换的用户，省略的话默认是root

切换用户有用exit可以退回上一个用户，也可以使用快捷键ctrl+d 。

使用普通用户切换到其他用户要输入密码，root用户切换到其他用户无需密码。

![2024-11-10-21-20-30.png](./images/2024-11-10-21-20-30.png)

### 1.2 sudo 临时以root身份执行
`sudo 其他命令`

长期使用root用户是不建议的，避免带来系统损坏，可以使用sudo命令，为普通的命令授权，临时以root身份执行。
不过不是所有用户都有权利使用sudo的，需要为普通用户配置sudo认证。

为普通用户配置sudo认证：
1、切换root用户执行visudo命令，会自动通过vi打开：/etc/sudoers
2、在文件最后添加 `cedar ALL=(ALL) NOPASSWD:ALL`  NOPASSWD:ALL表示使用sudo命令无需输入密码
3、wq保存退出
4、切换回普通用户

![2024-11-10-21-54-44.png](./images/2024-11-10-21-54-44.png)

![2024-11-10-21-55-41.png](./images/2024-11-10-21-55-41.png)

## 2 用户、用户组管理
Linux中关于权限的管控级别有2个，分别是：
- 针对用户的权限控制
- 针对用户组的权限控制

### 2.1 用户组管理
需要用root用户执行。

#### 2.1.1 创建用户组
`groupadd 用户组名`

#### 2.1.2 删除用户组
`groupdel 用户组名`

### 2.2 用户管理
#### 2.2.1 创建用户
`useradd 用户名 [-g -d]`
- -g：指定用户的组，不指定-g会创建同名组并自动加入，指定-g需要组已经存在
- -d：指定用户HOME路径，不指定，HOME目录默认在/home/用户名

#### 2.2.2 删除用户
`userdel [-r] 用户名`
- -r：删除用户的HOME目录，不使用-r，删除用户时，HOME目录保留

#### 2.2.3 查看用户所属组
`id [用户名]`

#### 2.2.4 修改用户所属组
`usermod -aG 用户组 用户名`

### 2.3 getent 查看当前系统有哪些用户和用户组

查看用户：
`getent passwd`

![2024-11-10-22-36-49.png](./images/2024-11-10-22-36-49.png)

共有7份信息，分别是：
用户名：密码(x)：用户ID：组ID：描述信息：HOME目录：执行终端（默认bash）

查看用户组：
`getent group`

![2024-11-10-22-39-36.png](./images/2024-11-10-22-39-36.png)

包含3份信息：
组名称：组认证（x）：组ID


## 3 查看权限控制
![2024-11-10-22-42-39.png](./images/2024-11-10-22-42-39.png)

依次表示 文件/文件夹权限控制信息、所属用户、所属用户组。

![2024-11-10-22-44-10.png](./images/2024-11-10-22-44-10.png)

![2024-11-10-22-46-57.png](./images/2024-11-10-22-46-57.png)

![2024-11-10-22-50-02.png](./images/2024-11-10-22-50-02.png)

## 4 chmod 修改权限控制
chmod可以修改文件、文件夹的权限信息，但是只有文件、文件夹的所属用户或root用户可以修改。

`chmod [-R] 权限 文件或文件夹`
- -R对文件夹内的全部内容应用同样的操作

```sh
chmod u=rwx,g=rx,o=x hello.txt
```
以上是将文件权限修改为rwxr-x--x，其中u表示user所属用户权限，g表示group组权限，o表示other其他用户权限。(逗号间不能有空格)

![2024-11-12-21-47-33.png](./images/2024-11-12-21-47-33.png)

（有颜色是因为系统提醒这个权限太危险了）

除了上述写法，还可以用数值表示：
```sh
chmod 751 hello.txt
```
![2024-11-12-21-50-42.png](./images/2024-11-12-21-50-42.png)

## 5 chown 修改所属用户/组
chown可以修改文件、文件夹所属用户和用户组。
`chown [-R] [用户][:][用户组] 文件或文件夹`
- -R同chmod
- 用户：修改所属用户
- 用户组：修改所属用户组
- ：用于分隔用户和用户组

```sh
chown root hello.txt  # 将hello.txt所属用户修改为root
chown :root hello.txt # 将hello.txt所属用户组修改为root
```

注意：普通用户无法修改所属为其他用户或组，所以这个命令只适用于root用户执行。


