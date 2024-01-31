---
title: .NET Core 配置HTTPS证书
date: 2024-01-31 10:01:24
tags: 
- 网站部署
- .NET Core
---



## 一、下载 Certbot 并安装

链接 ：[Github](https://link.zhihu.com/?target=https%3A//github.com/certbot/certbot/releases/latest/download/certbot-beta-installer-win_amd64_signed.exe)

下载完成后直接双击安装，安装完成后，以管理员方式运行cmd，输入cretbot --version，如果有显示就说明安装成功了

```cmd
C:\Windows\System32>certbot --version
certbot 2.8.0
```

命令行工具使用说明

```cmd
用法:
  certbot [子命令] [选项] [-d 域名] [-d 域名] ...
​
Certbot工具用于获取和安装 HTTPS/TLS/SSL 证书。默认情况下，Certbot会尝试为本地网页服务器
(如果不存在会默认安装一个到本地)获取并安装证书。最常用的子命令和选项如下:
​
获取, 安装, 更新证书:
    (默认) run       获取并安装证书到当前网页服务器
    certonly        获取或更新证书，但是不安装
    renew           更新已经获取但快过期的所有证书
   -d 域名列表        指定证书对应的域名列表，域名之间使用逗号分隔
​
  --apache          使用Apache插件进行身份认证和安装
  --standalone      运行一个独立的网页服务器用于身份认证
  --nginx           使用Nginx插件进行身份认证和安装
  --webroot         把身份认证文件放置在服务器的网页根目录下
  --manual          使用交互式或脚本钩子的方式获取证书
​
   -n               非交互式运行
  --test-cert       从预交付服务器上获取测试证书
  --dry-run         测试获取或更新证书，但是不存储到本地硬盘
​
证书管理:
    certificates    显示使用Certbot生成的所有证书的信息
    revoke          撤销证书(supply --cert-path)
    delete          删除证书
```

## 二、申请通配符证书的命令

打开cmd，执行以下命令

```cmd
certbot certonly -d "*.example.top" -d example.top --manual --preferred-challenges dns-01 --server https://acme-v02.api.letsencrypt.org/directory
```

命令说明：

- certonly 安装模式
- -d 申请证书的域名，如果是通配符域名输入 *.http://example.com
- –manual 使用交互式或脚本钩子的方式获取证书
- –preferred-challenges dns 使用 DNS 方式校验域名所有权
- –server，Let’s Encrypt ACME v2 版本使用的服务器不同于 v1 版本，需要显示指定

*.example.top换成你想要申请的域名，接下来，会提示需要进行手动验证DNS：

```cmd
Saving debug log to C:\Certbot\log\letsencrypt.log
Requesting a certificate for *.worldme.top and worldme.top

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name:

_acme-challenge.worldme.top.

with the following value:

HFxFyEX3aJSTsRlp_-aGCEE5z72oZeIt_TwMBq-8GHA

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

这里我的域名是腾讯云申请的，取域名解析手动添加一条TXT记录，按照提示操作，这里申请的是通配符域名，所以还有一级域名，就需要添加两条记录验证。

```cmd
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please deploy a DNS TXT record under the name:

_acme-challenge.worldme.top.

with the following value:

0YZruVLoJfAD7rUeWHZeenpA5UCUYw-2XvmzhkSUOio

(This must be set up in addition to the previous challenges; do not remove,
replace, or undo the previous challenge tasks yet. Note that you might be
asked to create multiple distinct TXT records with the same name. This is
permitted by DNS standards.)

Before continuing, verify the TXT record has been deployed. Depending on the DNS
provider, this may take some time, from a few seconds to multiple minutes. You can
check if it has finished deploying with aid of online tools, such as the Google
Admin Toolbox: https://toolbox.googleapps.com/apps/dig/#TXT/_acme-challenge.worldme.top.
Look for one or more bolded line(s) below the line ';ANSWER'. It should show the
value(s) you've just added.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Press Enter to Continue
```

根据操作完成后，在域名解析下就有两条TXT记录了，如下

![image-20240131101506208](/images/image-20240131101506208.png)

接下来就会显示申请成功的信息

```cmd
Successfully received certificate.
Certificate is saved at: C:\Certbot\live\worldme.top\fullchain.pem
Key is saved at:         C:\Certbot\live\worldme.top\privkey.pem
This certificate expires on 2024-04-30.
These files will be updated when the certificate renews.

NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

这里说明了证书的存放位置，进c盘去找，然后还有一个readme文件，打开里面提示是不要移动也不要修改名称

## 三、关于续签证书

刚刚申请证书成功的地方，有一条说明

```cmd
NEXT STEPS:
- This certificate will not be renewed automatically. Autorenewal of --manual certificates requires the use of an authentication hook script (--manual-auth-hook) but one was not provided. To renew this certificate, repeat this same certbot command before the certificate's expiry date.
```

大概意思为：

```text
下一个步骤:
—该证书不会自动更新。——manual证书的自动更新需要使用身份验证挂钩脚本(——manual-auth-hook)，但没有提供。要更新该证书，请在证书到期之前重复相同的certbot命令。
```

我就是如果下次续签，就是要重新申请一次。反正也不麻烦，大概几分钟就搞定了。

## 四、.NET Core使用证书

上面已经申请好了证书，我们直接复制出来使用就好，我这边.NET Core使用了Kestrel 服务器部署HTTPS，但是.NET Core目前不支持直接使用.pem文件进行HTTPS配置。所以，我们需要将.pem文件转换为.pfx（PKCS#12）格式，这是一种常用的用于存储证书和私钥的格式。

我们可以使用OpenSSL或其他证书管理工具将.pem文件转换为.pfx文件。

首先当然是安装openssl工具



然后，使用openssl将.pem文件转换为.pfx文件

```cmd
openssl pkcs12 -export -out certificate.pfx -inkey privatekey.pem -in certificate.pem
```

将privatekey.pem和certificate.pem替换为您的私钥和证书文件的实际文件名。运行此命令后，您将得到一个包含证书和私钥的certificate.pfx文件。

这里会需要输入一个密码，这个密码将用于配置，请保存。

获得了.pfx文件后，我们就可以将其用于配置HTTPS了，找到已经写好的程序，打开appsetting.json

```json
"Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://*:10001"
      },
	  "Https": {
        "Url": "https://*:10000",
		"Certificate": {
		  "Path": "D:\\certificate.pfx",
		  "Password": "123456"
		}
      }
    }
  }
```

这里我只截取了一部分关于Kestrel的配置，上面的HTTP是测试时候使用的，我们只需要将生成好的证书路径及生成时所使用的密码配置好，然后重启服务。整个步骤就完成了。
