# Midjourney设置

> **注意**
> webman ai基础版不支持绘图功能

**使用前提**
注册并订阅 MidJourney，**一定要创建自己的服务器和频道**，获取guild-id、channel-id、token、useragent [参考文档](https://www.workerman.net/a/1654)

**安装webman/midjourney**

```shell
composer require webman/midjourney
```

**设置**
打开webman目录下`config/plugin/webman/midjourney/process.php` `accounts` 和 `proxy`设置如下
```
'accounts' => [
    [
        'enable' => true,
        'token' => '上一步token实际值',
        'guild_id' => '上一步guild-id实际值',
        'channel_id' => '上一步channel-id实际值',
        'useragent' => '上一步user-agent实际值',
        'concurrency' => 3, // 并发数， 10/30刀账户填3，60/120刀填12
        'timeoutMinutes' => 10, // 任务提交mj后10分钟未响应则认为超时
    ]
],
'proxy' => [
    'server' => 'https://dis.imgin.top', // 海外用 https://discord.com
    'cdn' => 'https://cdn.imgin.top', // 海外用 https://cdn.discordapp.com
    'gateway' => 'wss://ws.imgin.top', // 海外用 wss://gateway.discord.gg
    'upload' => 'https://upload.imgin.top', // 海外用 https://discord-attachments-uploads-prd.storage.googleapis.com
],
```

设置完毕后一定要执行 `php start.php restart -d` 重启(reload不生效)
