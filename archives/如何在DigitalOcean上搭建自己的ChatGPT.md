本教程参考了多个网络资源，使用了开源项目 [Chatgpt-web](https://bit.ly/bewildcard)，经过亲测可以正常使用。本文将指导你如何在DigitalOcean上搭建ChatGPT服务器，且无需翻墙。

## 费用概览

以下是搭建过程中可能产生的费用：

- **DigitalOcean服务器**：每月 $4。注册后有 $200 的信用额度，有效期为 2 个月。
- **野卡 开卡费用**：$15。
- **OpenAI Token费用**：每 100,000 个 Token 收费 $0.04，大约对应 50,000 个汉字。

## 前提条件

为了顺利进行，请准备以下账户：

1. **DigitalOcean账户**
2. **OpenAI账户**

建议使用 [野卡](https://bit.ly/bewildcard)，因为OpenAI仅支持信用卡支付，而不接受中国信用卡。此外，在创建API密钥时需要电话号码验证，但中国手机号不支持。使用野卡，可以一步到位完成注册、验证及开卡。开卡费为 $15，充值费率为 3%。根据野卡的步骤完成后，请保存申请到的OpenAI API KEY，后续将用到。

## 开始搭建

### 一、创建DigitalOcean服务器

选择新加坡数据中心，并且操作系统选择 CentOS 8。

**CPU选项**：个人使用建议选择最低配的 $4/月方案。

在身份验证方法中，选择 **SSH Key**。DigitalOcean的控制台提供有创立SSH Key的相应教程。

点击 **Create Droplet**，等待服务器成功创建。成功后，记下显示的服务器IP（红框部分），以备后用。

### 二、服务器安装Docker

打开服务器的终端，点击 **Access Console**。

首先，安装Docker，参考下述步骤，已亲测可用：

1. 更新yum：
   bash
   yum update
   

2. 下载docker-ce的repo：
   bash
   curl https://download.docker.com/linux/centos/docker-ce.repo -o /etc/yum.repos.d/docker-ce.repo
   

3. 安装依赖（可能需要一路输入 y）：
   bash
   yum install https://download.docker.com/linux/fedora/30/x86_64/stable/Packages/containerd.io-1.2.6-3.3.fc30.x86_64.rpm
   

4. 安装docker-ce：
   bash
   yum install docker-ce
   

5. 启动docker：
   bash
   systemctl start docker
   

6. 设置docker开机启动：
   bash
   systemctl enable docker
   

7. 安装docker-compose：
   bash
   sudo wget https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m) -O /usr/local/bin/docker-compose
   

   如果遇到错误 `sudo: wget: command not found`，请先安装wget：
   bash
   yum -y install wget
   

8. 添加操作权限：
   bash
   sudo chmod +x /usr/local/bin/docker-compose
   

9. 设置快捷方式：
   bash
   sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
   

10. 查看docker-compose版本：
    bash
    docker-compose --version
    

Docker安装完成。

### 三、服务器部署ChatGPT

以下是部署程序：

1. 创建 `docker-compose.yml` 文件：

   - 首先，创建一个目录：
     bash
     mkdir chatgpt_web && cd chatgpt_web
     

   - 创建docker-compose.yml文件：
     bash
     vim docker-compose.yml
     

     如果遇到 -bash: vim: command not found 错误，请安装vim：
     bash
     yum -y install vim*
     

   - 填写并保存如下内容到配置文件中：
     yaml
     version: '3'
     services:
       app:
         image: chenzhaoyu94/chatgpt-web:latest
         ports:
           - 3002:3002
         environment:
           # API密钥
           OPENAI_API_KEY: sk-xxx（请修改为你申请的密钥）
           # 超时，单位毫秒，可选
           TIMEOUT_MS: 60000
     

   注意：在编写yml文件时，要确保删除注释以避免错误。

2. 部署并启动运行：
   bash
   docker-compose up -d
   

3. 登录ChatGPT页面：
   
   运行成功后，在浏览器中访问（确保3002端口已开放）：
   
   http://<你的服务器IP>:3002
   

   其中`<你的服务器IP>`是之前保存的IP地址。

4. 其他问题解决：

   - 如果遇到 fetch failed，可以尝试点击上面的刷新按钮。如果刷新无效，可以重启docker，然后重新启动服务。

👉 [野卡 | 一分钟注册，轻松订阅海外线上服务](https://bit.ly/bewildcard)