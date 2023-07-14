---
title: git配置公钥
tags:
  - git
categories:
  - git
toc: true
recommend: 1
keywords: categories-java
uniqueId: 2023-07-14 08:14:32/git配置公钥.html
mathJax: false
date: 2023-07-14 16:14:32
---
> 记录git配置
<!-- more -->

##### 生成本机公钥

```sh
cd ~/.ssh
ssh-keygen -t rsa -C "邮箱地址"
```

##### github 创建 SSH key

Settings -> SSH and GPG keys -> New SSH key -> Add SSH key

[Add new SSH keys (github.com)](https://github.com/settings/ssh/new)
