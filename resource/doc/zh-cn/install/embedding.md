# 训练模块部署步骤
假设你已经安装了webman/ai

### 步骤一：安装redis-stack服务端（注意普通redis-server服务端不支持，要用redis-stack才行）
```
docker pull docker.1ms.run/redis/redis-stack
mkdir /home/data/redis -p
docker run --name redis-stack -v /home/data/redis:/data -p 6380:6379 -d redis/redis-stack
```

### 步骤二：给webman安装illuminate/redis组件

**webman-v2版本 webman根目录执行**
```
composer require -W webman/redis illuminate/events
```

**webman-v1版本 webman根目录执行**
```
composer require -W illuminate/redis illuminate/events
```

### 步骤三：配置redis
新建 `plugin/ai/config/redis.php`，内容如下
```
<?php
return [
    'default' => [
        'host' => '127.0.0.1',
        'password' => null,
        'port' => 6380,
        'database' => 0,
    ],
];
```

### 步骤四：重启webman
Linux: webman根目录运行 `php start.php restart -d`
Windows: 按`ctl c`停止webman，然后运行 `php windows.php start`

# 训练过程

### 新增训练集

![截图](https://www.workerman.net/upload/img/20231213/136579162dae2c.png)

### 导入数据

![截图](https://www.workerman.net/upload/img/20231213/13657916917daa.png)

> **提示**
> 如果提示 Class'Redis' not found 是因为没有给PHP安装Redis扩展。


**训练的文本样例**
![截图](https://www.workerman.net/upload/img/20231217/17657eae334157.png)

**注意：训练内容没有固定格式，训练的数据最好顶部加上一个对本内容的总结性标题或提问，这样效果才会最好。类似上面这个截图**

[完整的webman手册样本数据，点击这里下载](https://www.workerman.net/embedding-webman-manual.zip)


> **提示**
> 导入数据目前只支持txt和md为后缀的文件，单个文件大小不能大于9k。支持压缩包上传，同理压缩包内每个文件大小不能大于9k。


### 开始训练
![截图](https://www.workerman.net/upload/img/20231213/1365791715340f.png)


![截图](https://www.workerman.net/upload/img/20231213/1365791866af7d.png)

AI训练数据 里的 内容向量 里有值，说明此条记录已经训练完毕，内容向量 同时会在redis-stack里存储一份，用来快速检索。

> **注意**
> 如果你不想让你训练的模型回复训练内容以外的数据，可以在角色提示词里明示。例如webman手册助手的角色提示词如下：
“webman是一个高性能php框架，你是一个webman助手，以下是webman文档，请根据文档回复，如果无法得到答案或者不是webman相关的问题则回复文档中未找到对应的答案。请确保回复正确。”

### 设置角色
![截图](https://www.workerman.net/upload/img/20231213/1365791972b73e.png)
在需要使用训练数据的角色里选择训练集，保存。(前端用户需要刷新页面角色才能生效)
AI就会使用训练集里的数据回复问题。

### 测试结果

![截图](https://www.workerman.net/upload/img/20231213/136579bbc07f08.png)

![截图](https://www.workerman.net/upload/img/20231213/136579bb85156f.png)
