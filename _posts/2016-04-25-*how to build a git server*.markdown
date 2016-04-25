---
layout:  post
title: how to build a git server
date: 2016-04-25
---
背景：
  我的代码依赖于服务器，测试离不开服务器的环境，但是服务器上的编程工具比较落后，
  个性化不够强，但是服务器是团队成员一起使用的，不宜更改，于是就想在服务器创建
  一个自己的远程仓库做代码管理，开发在自己本地上开发，再更新到远程仓库，最后在
  服务器工程目录里拉取仓库里最新代码进行测试。

条件：
  两台可以ping通的机器A和机器B，机器B为远程仓库所在机器。两者装有git和ssh服务。  

1. 创建独立的账户进行代码版本控制(当前用户为root，新用户取名为git)  
    adduser git  
    passwd git  
    su git  
    cd  
    mkdir .ssh  

2. 回到root用户，创建公钥并授权
    cd  
    ssh-keygen(按enter键取默认值)  

3. 切换git用户，将root用户的公钥追加到自己的认证主机文件authorized_keys中
    cat /root/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys  

4. 更改ssh配置
    vi /etc/ssh/sshd_config  将下面这条的注释去掉  
    \# AuthorizedKeysFile      %h/.ssh/authorized_keys  

5. 在服务器中部署项目
    su git  
    mkdir -p ~/opt/git/yourproject.git  
    cd ~/opt/git/yourproject.git  
    git init --bare --shared  

现在root用户就可以对git用户下的远程仓库进行pull/push操作了，前面所说的是在
机器B上不同用户之间的拉取，要想在机器A上录取机器B上git用户下的远程仓库，
只需要**将机器A的公钥追加到机器B上/home/git/.ssh/authorized_keys**即可。
