# Nginx

Nginx 是一款轻量级的 Web 服务器、反向代理服务器以及电子邮件代理服务器，其特点是占用内存少，并发能力强，在同类型的网页服务器中表现优秀



## 安装

安装

> http://nginx.org/en/linux_packages.html

操作指令

```
# 启动Nginx
nginx [-c configpath]
# 信息查看
nginx -V
nginx -V 详细
# 控制Nginx
nginx -s signal
		 stop 快速关闭
		 quit 优雅的关闭
		 reload 重新加载配置
		 
#通过系统管理
systemctl status nginx 查看nginx状态
systemctl start nginx 启动nginx服务
systemctl stop nginx 关闭nginx服务
systemctl enable nginx 设置开机自启
systemctl disable nginx 禁止开机自启
不推荐，因为通过systemctl启动服务无法加载配置文件
```

nginx启动后默认有两个进程

master process主进程

worker process工作进程



## Nginx配置文件

Nginx配置文件包含指定指令控制的模块。
指令分为简单指令和块指令
一个简单指令由名称和参数组成，以空格分隔，并以分号结尾
一个块指令和简单指令具有相同的结构，但不是以分号结束，而是以一个大括号包围的一堆附
加指令结束
如果一个大括号内可以有其他的指令，它就被称为一个上下文，比如（events,http,server,location）
指令

```
nginx -t 不运行，仅测试配置文件
nginx -c configpath 从指定路径加载配置文件
nginx -t -c configpath 测试指定配置文件
```

/etc/nginx 配置文件地址

创建自己的配置文件

location root表示根目录

```
.etc/nginx/nginx.conf > ~/home/kzq
cd conf.d/
cat default.conf 将default.conf中的内容复制到nginx.conf中

```



```
nginx -t -c /home/kzq/nginx.conf  使用绝对路径!
```





# Nginx的作用

## 反向代理

反向代理（Reverse Proxy）方式是指以代理服务器来接受internet上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给internet上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

简单来说就是真实服务器不能直接被外部网络访问，所以需要一台代理服务器，而代理服务器能被外部网络访问的同时又跟真实服务器在同一个网络环境，也可能反向代理服务器和真是服务器是同一台服务器，但是端口不同。



## 负载均衡

负载均衡是指将负载分摊到多个操作单元上进行执行，例如Web服务器、FTP服务器、企业关键应用服务器和其它关键任务服务器等，从而共同完成工作任务。

简单而言就是当有2台或以上服务器时，根据规则随机的将请求分发到指定的服务器上处理，负载均衡配置一般都需要同时配置反向代理，通过反向代理跳转到负载均衡。而Nginx目前支持自带3种负载均衡策略，还有2种常用的第三方策略。



## HTTP服务器

Nginx本身也是一个静态资源的服务器，当只有静态资源的时候，就可以使用Nginx来做服务器，同时现在也很流行动静分离，就可以通过Nginx来实现。



## 正向代理

正向代理，意思是一个位于客户端和原始服务器(origin server)之间的服务器，为了从原始服务器取得内容，客户端向代理发送一个请求并指定目标(原始服务器)，然后代理向原始服务器转交请求并将获得的内容返回给客户端。客户端才能使用正向代理。当你需要把你的服务器作为代理服务器的时候，可以用Nginx来实现正向代理。