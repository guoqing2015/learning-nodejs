# nginx日志管理



我们观察nginx的server段,可以看到如下类似信息
```
server {
    access_log  logs/host.access.log  main;
}
 ```

这说明 该server, 它的访问日志的文件是  `logs/host.access.log` , 使用的格式”`main`”格式. 除了main格式,你可以自定义其他格式.

main格式是什么?

```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';
```

main格式是我们定义好一种日志的格式,并起个名字,便于引用.  
以上面的例子, main类型的日志,记录的 remote_addr.... http_x_forwarded_for等选项.



## 日志格式

日志格式 是指记录哪些选项  

默认的日志格式: `main`
```
     log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                            '$status $body_bytes_sent "$http_referer" '
                            '"$http_user_agent" "$http_x_forwarded_for"';
```

如默认的main日志格式,记录这么几项  
远程IP- 远程用户/用户时间 请求方法(如GET/POST) 请求体body长度 referer来源信息  
`http-user-agent`用户代理/蜘蛛 ,被转发的请求的原始IP  

`http_x_forwarded_for`:在经过代理时,代理把你的本来IP加在此头信息中,传输你的原始IP



## 声明一个独特的 log_format 并命名
```
    log_format  mylog '$remote_addr- "$request" '
                     '$status $body_bytes_sent "$http_referer" '
                        '"$http_user_agent" "$http_x_forwarded_for"';
```                        
在下面的server/location,我们就可以引用 `mylog`

在server段中,这样来声明  
Nginx允许针对不同的server做不同的Log ,(有的web服务器不支持,如lighttp)

```
access_log logs/access_8080.log mylog;   
声明log     log位置              log格式;
```

