---
layout: post
title:  "Submit Linux Patch"
tags:   Linux
date:   2024-05-11 08:54:35 +0800
categories: [Linux] 
---

# 提交Linux Patch
客户突然要求提供Linux Driver，赶鸭子上架。完成了客户的功能。 但是要求将该驱动上传到社区。坑不少，这里记录下。

## 前置准备

### Git

获取源码、生成Patch、发送Patch到社区 离不开Git 

- 设置邮箱

    ```shell
    # 找到.gitconfig 文件
    [sendemail]
            smtpserver = smtp.googlemail.com  ## 服务器
            smtpuser = xxx@gmail.com  #自己的邮箱
            smtpserverport = 587
            smtpencryption = tls
    ```
    这里有个巨坑的地方，以公司名义写的驱动，肯定是用公司的邮箱。
    IT 说Pop3 这些端口给封了，让我先复制到`Outlook`发。 `Outlook` 发送邮件加密，`Message ID` 全乱了， 被社区的`Krzysztof` diss 了一把。
    最后找老大，在HR 系统申请，经过层层审批，才拿到权限，并有IP限制。
    
    如果邮箱服务器是公司自建的，这个服务器地址一定要申请。

- 下载源码
    Linux 源码下载要下 子系统管理员的`git`。千万不要直接`git clone`, 太大也太慢，你有梯子另说。

    在Linux 仓库中查找子系统管理员的[git](https://git.kernel.org/). 比如我这次弄的是Led 子系统，`git://git.kernel.org/pub/scm/linux/kernel/git/lee/leds.git`.
    ```shell
    # 最新的Code 的地址都在for-next 分支上
    git clone -b for-leds-next --single-branch --depth 1 git://git.kernel.org/pub/scm/linux/kernel/git/lee/leds.git
    
    ```  
- 