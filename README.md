# V2Ray Heroku

>Fork from [v2ray-heroku](https://github.com/bclswl0827/v2ray-heroku)

## 概述

用于在 Heroku 上部署 V2Ray Websocket。

**Heroku 为我们提供了免费的容器服务，我们不应该滥用它。**

**可以部署两个以上的应用，实现[负载均衡](https://toutyrater.github.io/app/balance.html)，避免长时间大流量连接某一应用而被 Heroku 判定为滥用。**

**Heroku 的网络有时候并不稳定，部署前请三思。**

**Heroku 每月使用限额 550 小时，绑定信用卡后 1000 小时，可覆盖单个应用整个月的使用。流量单应用限制 2TB 每月。**

## 镜像

本镜像仅 6MB，比起其他用于 Heroku 的 V2Ray 镜像，不会因为大量占用资源而被封号。

[![Deploy](https://www.herokucdn.com/deploy/button.png)](https://dashboard.heroku.com/new?template=https%3A%2F%2Fgithub.com%2Fgamekingv%2Fv2-heroku)

## ENV 设定

### UUID

`UUID` > `一个 UUID，供用户连接时验证身份使用`。

部署完毕后建议在 Heroku 上修改 `UUID`。

## 注意

WebSocket 路径和 AlterID 在 configure.sh 文件中，自行修改。

V2Ray 将在部署时自动安装最新版本。

**出于安全考量，除非使用 CDN，否则请不要使用自定义域名，而使用 Heroku 分配的二级域名，以实现 V2Ray Websocket + TLS。**

## 使用 Cloudflare 加速

登陆 [Cloudflare](https://www.cloudflare.com/) 点击创建 Workers，并复制下面的复制脚本进去，修改其中的 host 值为 Heroku 服务链接，不包含 `https://`，path 改为 WebSocket 路径，包含 `/`。

```javascript
const host = '应用名称.herokuapp.com';
const path = '/path';

addEventListener(
  "fetch", event => {
    const url = new URL(event.request.url);
    if (url.pathname === path) {
     url.hostname = host;
      const request=new Request(url,event.request);
      event.respondWith(
        fetch(request)
      )
    }
    else {
      event.respondWith(
        new Response('Not Found', { status: 404 })
      )
    }
  }
)
```
可是使用 [badafans/better-cloudflare-ip](https://github.com/badafans/better-cloudflare-ip) 来测试最佳 IP。

使用 v2rayN 时将 Cloudflare 域名填进伪装域名，测试获得的最佳 IP 填进地址即可。
