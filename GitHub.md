# 目录

- [使用GitHub](#使用GitHub)

# 使用GitHub

## 1 第一次提交

    …or create a new repository on the command line
    echo "# notes" >> README.md
    git init
    git add README.md
    git commit -m "first commit"
    git remote add origin https://github.com/lucistang/notes.git
    git push -u origin master
    …or push an existing repository from the command line
    git remote add origin https://github.com/lucistang/notes.git
    git push -u origin master

## 使用HTTPS协议，在远程地址中带上密码。

    git remote set-url origin https://LucisTang:Tang124816@github.com/lucistang/notes.git


## [使用SSH连接到GitHub](https://help.github.com/en/github/authenticating-to-github/connecting-to-github-with-ssh)

## Generating a new SSH key and adding it to the ssh-agent
1.Open Git Bash.

    ssh-keygen -t rsa -b 4096 -C "LucisTang2020@gmail.com"

    lucis Tang124816

    eval $(ssh-agent -s)

    ssh-add ~/.ssh/lucis

    clip < ~/.ssh/lucis.pub

2.Testing your SSH connection

    ssh -T git@github.com

## 提交
1.取消global

    git config --global --unset user.name
    git config --global --unset user.email

2.设置每个项目repo的自己的user.email

    git config  user.email "LucisTang2020@gmail.com"
    git config  user.name "LucisTang"

3.提交

    git add .
    git commit -m "first commit"
    git push -u origin master