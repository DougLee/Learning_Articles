title: "使用ngrok为内网绑定一个公网域名"
date: 2015-12-31 16:36:52
tags:
- ngrok
---

## ngrok是什么

ngrok是一个反向代理，通过在公共的端点和本地运行的服务器之间建立一个安全的通道。ngrok可以获取和分析所有通道上的流量，方便于调试和后期分析。

## ngrok的使用场景

作为一个Web开发者，我们有时候会需要临时地将一个本地的Web网站部署到外网，以供它人体验评价或协助调试等等，为了达到这个目的我们需要找到一台运行于外网的Web服务器并将网站部署到服务器上。可是你不一定会有运行于外网的web服务器，这时候ngrok就可以派上用场了，除此之外还有下面这些时候你会用到ngrok
<!-- more -->

1. 开发微信公众账号，需要申请或者调试的时候
2. 申请weibo或者qq开发者账号，新建应用需要提供域名的时候(我只是想要获取一个key和id在本地调试一下而已，非让我验证域名。。)

## 使用ngrok

1. 下载ngrok，在[https://ngrok.com/download](https://ngrok.com/download)，选择需要的版本下载。
2. 注册ngrok，网址：[https://dashboard.ngrok.com/user/signup](https://dashboard.ngrok.com/user/signup)，注册是为了得到一个Author token，当然不注册也可以使用ngrok，只是映射的二级域名是随机的。
3. 把下载好的ngrok解压，并用windows命令窗口执行如下命令

```shell

ngrok authtoken yourtoken 5000

ngrok -subdomain=test 5000

```
这时候在访问，http://test.ngrok.io，会发现和访问http://localhost:5000的效果一样

如果想要支持指定顶级域名的话需要付费。
