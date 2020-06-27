# vscode配置
## 显示
### 显示空格
    查看-渲染空格
### 字体
    Hack Fonts WindowsInstaller
    设置中查找fontfamily关键字，然后更换为hack

## 插件
### git history
### visual studio intellicode
### bracket pair colorizer 2
### python yapf

# 文件管理
## [SVN 服务器](#CentOS-7-安装配置SVN服务器)
## [SVN 客服端](#Windows安装配置SVN客服端)
## [SVN 同步](#CentOS-SVN-同步)
## [配置SVN多项目](#CentOS-7-配置SVN多项目)

# 远程控制
## PuTTy
## WinSCP





## CentOS 7 安装配置SVN服务器

第一步： 安装SVN服务端

    # yum install -y subversion

第二步： 创建SVN版本库

    # mkdir -p /home/svn/project
    # svnadmin create /home/svn/project/

第三步： 配置SVN信息

进入版本库中的配置目录conf，此目录有三个文件：  
authz               #权限配置文件   
passwd              #用户名口令文件  
svnserve.conf       #svn服务综合配置文件  
配置权限配置文件authz  

    # vi /home/svn/project/conf/authz

[groups]              
#用户组  
admin = admin,root,test    
#用户组所对应的用户  
[/]                 
#库目录权限  
@admin = rw           
#用户组权限   
*=r               
#非用户组权限  

配置用户口令文件passwd  

    # vi /home/svn/project/conf/passwd

[users]  

    # harry = harryssecret
    # sally = sallyssecret
    admin = Admin1234
    root = root1234
    test = test1234
    --用户名以及所对应的密码--

配置 svn服务综合配置文件svnserve.conf  

    # vi /home/svn/project/conf/svnserve.conf

    [general]
    #匿名访问的权限，可以是read,write,none,默认为read
    anon-access=none
    #使授权用户有写权限
    auth-access=write
    #使用哪个文件作为账号文件
    password-db=passwd
    #使用哪个文件作为权限文件
    authz-db=authz
    #认证命名空间，subversion会在认证提示里显示，并且作为凭证缓存的关键字
    realm=/home/svn/project
    添加，也可以修改文档默认内容，看个人喜好。

第四步： 启用SVN，连接SVN以及关闭SVN

    # svnserve -d-r/home/svn/project/
    # ps aux|grep svn      #查看SVN是否启动成功

如成功启动，则会有以下类似信息

    root      2402  0.0  0.0 162164   648 ?        Ss   May16   0:15 svnserve -d -r /home/svn/project/

第五步： 配置防火墙开放3690端口

CentOS 7 默认Firewalld防火墙配置

    # firewall-cmd --zone=public --add-port=3690/tcp --permanent
    # firewall-cmd --zone=public --add-port=3690/udp --permanent
    命令含义：

    --zone #作用域
    --add-port=80/tcp #添加端口，格式为：端口/通讯协议
    --permanent #永久生效，没有此参数重启后失效

    注意：记得重启防火墙

    # firewall-cmd --reload

    iptables防火墙配置

    -A INPUT -p tcp -m state --state NEW -m tcp --dport 3690-j ACCEPT
    -A INPUT -p udp -m state --state NEW -m udp --dport 3690-j ACCEPT

第六步： 配置开机启动SVN

    # vi /etc/rc.d/rc.local

    添加内容

    svnserve -d -r /data/svn/project/

注意：升级CentOS7后，原来习惯向/etc/rc.d/rc.local直接添加启动脚本/程序的方法无效了，这是需要给/etc/rc.d/rc.local赋予可执行权限


    # chmod +x /etc/rc.d/rc.local

至此，CentOS的SVN配置完毕。



## Windows安装配置SVN客服端

[TortoiseSVN 使用教程](https://www.runoob.com/svn/tortoisesvn-intro.html)

 URL: svn://10.0.4.17:3690


 ## CentOS SVN 同步

 第一步： 安装SVN服务端

    # yum install -y subversion

第二步： 创建SVN版本库

    # mkdir -p /home/svn/project
    # svnadmin create /home/svn/project/

    svnsync initialize file:///home/svn/project/ svn://207.148.17.10/opt/svn/db

    svnsync synchronize file:///home/svn/project/

ps:

    scp -r -p 207.148.17.10:/opt/svn/db/conf /home/svn/project

    6.在源库服务器上源库的hooks中创建

post-commit.bat 文件

输入

    svnsync sync --non-interactive https://192.168.11.164/svn/test--sync-username svn用户名 --sync-password svn密码 
(前面建议过，最好两台服务器svn账户密码一样，这样操作起来方便)


    10.svn主从
    server1: 192.168.1.2
    server2: 192.168.1.3
    首先在sever1和sever2上搭建好svn，现在的目的是将server1同步备份到server2
    1)修改hooks文件：
    #cp /var/svn/svnrepos/hooks/pre-revprop-change.tmpl /var/svn/svnrepos/hooks/pre-revprop-change
    #cat /var/svn/svnrepos/hooks/pre-revprop-change
    #!/bin/bash

    echo "Changing revision properties other than svn:log is prohibited" >&2
    exit 0
    2)在server1上直接运行:
    #svnsync init svn://192.168.1.2/var/svn/svnrepos svn://192.168.1.3/var/svn/svnrepos --username test --password test
    3)上一步成功之后，以后就可以直接同步了：
    #svnsync sync svn://192.168.1.2/var/svn/svnrepos
    4)为了让server1每次有更新之后都自动同步到server2，可以在server1的commit的hooks最后加上执行一下同步的命令(或者使用脚本定时执行)：
    #svnsync sync svn://192.168.1.2/var/svn/svnrepos


## CentOS 7 配置SVN多项目

    mkdir -p /home/svn/PrivateLibrary
    svnadmin create /home/svn/PrivateLibrary/

    # svnserve -d -r /home/svn/
    # ps aux|grep svn      #查看SVN是否启动成功

    ps -ef | grep svn # 查看SVN运行，找到相应进程ID
    kill -9 进程ID #杀掉进程

 URL: svn://207.148.17.10/PrivateLibrary