# 完整的安装步骤

## 环境要求
php>=7.4, MySQL>=5.5

## 安装步骤

1. 安装[composer](https://getcomposer.org/download/) （已经安装过的忽略）

2. 运行命令 `composer create-project workerman/webman` 创建webman项目

3. 进入到webman目录安装 webman-admin 及其它所需组件

```
cd webman
composer require -W webman/admin webman/openai
```

4. 重启webman
   `php start.php restart -d`

5. 进入webman/admin安装向导
   访问 `http://127.0.0.1:8787/app/admin` 完成webman/admin的安装

6. 进入webman/admin后台安装`用户模块`和`webman AI助手`
   ![截图](https://www.workerman.net/upload/img/20230823/2364e56e9f955f.png)

7. 新增apikey
   ![截图](https://www.workerman.net/upload/img/20240318/1865f7a85153b3.png)

8. 至此webman/ai助手安装完毕
   plus版本访问地址 `http://127.0.0.1:8787/app/ai`  
   基础版本访问地址 `http://127.0.0.1:8787/app/gpt`  


# nginx配置参考(非宝塔用户)

如果你需要配置域名访问，请参考下面配置

```
server {
  server_name 站点域名;
  listen 80;
  access_log off;
  proxy_buffering off;
  root /your/webman/public;

  location ^~ / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_http_version 1.1;
      proxy_set_header Connection "";
      if (!-f $request_filename){
          proxy_pass http://127.0.0.1:8787;
      }
  }
}
```
一般来说只需要更改server_name和root即可，其他默认。

# nginx配置参考(宝塔用户)

![截图](https://www.workerman.net/upload/img/20231220/206582f5616e52.png)

如图在伪静态里添加如下配置
```
  proxy_buffering off;
  location ^~ / {
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_http_version 1.1;
      proxy_set_header Connection "";
      if (!-f $request_filename){
          proxy_pass http://127.0.0.1:8787;
      }
  }
```

