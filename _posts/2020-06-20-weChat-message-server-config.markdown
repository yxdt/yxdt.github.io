---
layout: post
title: 小程序消息服务器配置.Net Core实现
date: 2020-06-20 10:12:29 +0800
categories: [Fullstack, dotNet]
tag: [dotNet, weChat, C#]
---

# {{page.title}}

微信小程序的消息功能允许订阅了消息的小程序用户接收消息，极大地提升了小程序地可用性，微信官方文档对服务器端配置给出了 PHP 的例子，对于.Net 开发人员，只需要简单的转化即可。
下面就给出相应的实现：

1 打开 Visual Studio， 创建一个新项目选择“.Net Core Web 应用程序”， 然后确定 ASP.NET Core 版本，再选择 API。

![创建新Web API项目](/assets/images/webapi.png)

<!--more-->

2 生成新的 ApiController，并添加新的 HttpGet 请求处理方法：

![添加新API控制器](/assets/images/webapi1.png)

3 验证方法的具体实现：

```c#
[HttpGet]
public string Get([FromQuery]string signature,
                   [FromQuery]string timestamp,
                   [FromQuery]string nonce,
                   [FromQuery]string echostr)
{
    var token = "put-your-token-here";
    var tmpArr = new String[] { token, timestamp, nonce };
    var sorted = String.Join("",tmpArr.OrderBy(x => x));
    SHA1 sha1 = System.Security.Cryptography.SHA1.Create();
    var bytes = sha1.ComputeHash(Encoding.UTF8.GetBytes(sorted));
    StringBuilder sb = new StringBuilder();
    for (int i = 0; i < bytes.Length; i++)
    {
        sb.Append(bytes[i].ToString("x2"));
    }
    if(String.Compare(sb.ToString(), signature) == 0)
    {
        return echostr;
    }
    return "error";
}
```
