# 常见问题

### 国内服务器可以部署么？
可以，不需要额外设置，可以直接部署运行即可

### 如何设置不需要会员就可以使用
进入管理后台->AI模型页面，找到你想要免费开放的模型，点击编辑按钮，在`每日赠送`一栏里填写每个用户每天可以免费使用的数量即可

### 我想在访问域名时直接进入AI页面
目前是访问 `https://我的域名.com/app/ai` 进入AI页面，如果想访问`https://我的域名.com`时直接进入AI页面，在 `config/route.php`中增加如下路由配置并执行 `php start.php reload`
```php
Route::any('/', [plugin\ai\app\controller\IndexController::class, 'index']);
```
### 8787端口访问超时
云服务器需要在安全组开放8787端口，如果有使用宝塔，宝塔面板里也要开放8787端口。(如果使用了nginx代理，则不需要开放8787端口)

### 为什么没有打字效果？
如果你使用了nginx，请注意nginx里加上配置 `proxy_buffering off;`

### 为什么不显示回复？
一般是nginx代理问题，请参考本文档设置nginx代理

### 源码是否可以二次售卖？
禁止二次出售此源码，严禁将源码泄露给第三方，一旦发现将追究法律责任，并收回授权。

### 如何开启强制登录功能
在webman/admin管理后台->AI助手->AI通用设置里设置

### 提示 You didn't provide an API key. You need to provide your API key in an Authorization header using Bearer auth ...
请在webman/admin管理后台 `AI助手` > `ApiKey设置` 添加里添加ApiKey

### 提示 ErrorException: stream_socket_client(): php_network_getaddresses: getaddrinfo for 具体域名.com failed: Name or service not known in
原因是你的服务器无法解析 `具体域名.com`，此时需要在服务器上配置一条host。
linux服务器打开 `/etc/hosts` (windows为C:\Windows\System32\drivers\etc\hosts) 增加一条
`具体ip 具体域名.com`，其中具体ip可用过命令 `ping 具体域名.com`得到

### 如何升级
在webman/admin后台插件管理页面找到webman/ai插件，点击升级按钮

### 自己部署代理
如果觉得第三方代理比较慢，想自己搭建一个，可以参考这个分享[webman作为http代理](https://www.workerman.net/a/1567)
