title: 管理多个SSH Key以支持多个Git账号
date: 2016-04-20 09:30:02
tags:
  - ssh
  - git
---
SSH Key 可以方便地解决系统登录的问题，不用每次提交代码时输入相关Git平台的登录密码。

由于各种各样的原因，程序猿一般都有多台电脑或多个开发系统环境，同样也有多个Git账户，最起码的可能有一个GitHub账户加公司内部的一个GitLab账户。可能不同的账户有不同的用户名，邮箱等。

## 1. 生成SSH密钥
* #### 生成默认公钥
先确认系统中是否已有默认的SSH密钥（使用的是公钥，之后都称公钥）存在。

```
cat ~/.ssh/id_rsa.pub
```

如果已经有了则不用生成默认的公钥了，如果没有，则按照如下命令生成。生成后再用上述`cat`命令查看公钥。

```
ssh-keygen -t rsa -C "$your_email"
```

将上述的`$your_email`替换为你想使用的默认email地址。如我在工作电脑上生成默认公钥时邮箱使用的是公司内部邮箱。在私人电脑上则用的是本人Gmail邮箱。

<!--more-->
* #### 生成更多的公钥
按如下命令生成新的公钥，注意这次要天加输出文件的名字，以免覆盖默认公钥。

```
ssh-keygen -t rsa -C '$another_email' -f github
```

如需更多的SSH公钥按上述命令生成即可。**注意输出文件名字不要相同，无需加`.pub`后缀**。

## 2. 多个操作系统用SSH Key访问同一个Git平台
最常见的情况是我们需要在“工作电脑”和“私人电脑”中都能够方便的向同一个GitHub仓库提交代码。

进入`.ssh`目录，把该目录下的`github`以及`github.pub`拷贝至其他系统中的`.ssh`目录下，然后将公钥的内容复制，添加到GitHub的账户即可。gitlab、coding.net等平台类似。

除了拷贝的方式共用一份公钥，也可以在不同的系统里另外生成公钥，然后再逐个添加至Git平台。

## 3. 同一个操作系统用不同的SSH公钥访问不同的Git平台
* #### 创建ssh配置文件
```
touch ~/.ssh/config
```

* #### 添加ssh配置
```
Host    github                              # ssh别名，随意取
    HostName        github.com              # 主机名
    Port            22                      # SSH端口号
    PreferredAuthentications publickey      # 认证类型
    User            git                     # 用户
    IdentityFile    ~/.ssh/github
```

将上述配置内容添加至 `~/.ssh/config` 文件即可。注意按需修改配置内容，勿一成不变地复制。要添加更多的Git平台账户时继续在config文件中按上述格式追加配置即可。

## 4. 遇到的问题
当添加配置完成后通过如下命令验证是否可以用公钥访问：

```
ssh -T git@github.com   # 这里的github取自上述 config 文件中的 Host 值
```

* #### 配置文件权限错误
提示如下错误：

```
Bad owner or permissions on /home/vagrant/.ssh/config
```

原因是config文件的所有者或权限有问题。按如下命令修复：

```
cd ~/.ssh/
chmod 600 *
```

再执行`ssh -T github`得到如下结果：

```
Hi denglj! You've successfully authenticated, but GitHub does not provide shell access.
```

* #### 访问github无权限
请参考资料：https://help.github.com/articles/error-permission-denied-publickey/
