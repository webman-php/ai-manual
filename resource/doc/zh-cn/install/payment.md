# 开启支付
![截图](https://www.workerman.net/upload/img/20230823/2364e5675a397d.png)
如果要显示会员支付栏目(如图红框)，必须配置支付宝或者微信。

### 配置方法
进入webman目录安装 `yansongda/pay`
PHP7.4用户执行命令
`composer require -W yansongda/pay ~3.3.0`

PHP8用户执行命令
`composer require -W yansongda/pay ~3.5.0`

新建 `plugin/ai/config/payment.php`，内容参考`payment.example.php`

重启webman `php start.php restart -d`

### 支付宝支付相关参考
**详细支付宝配置教程参考 https://www.workerman.net/a/1564**

### 微信支付相关参考
**详细微信配置教程参考 https://www.workerman.net/a/1613**
