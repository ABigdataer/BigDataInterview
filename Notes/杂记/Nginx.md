## Nginx

    特点 ： 自由、开源、高性能、轻量级Http服务器、反向代理服务器、配置简单、作为web服务器性能和稳定性超越Apche(能支持5万并发操作)
    
#### 1、反向代理

    以代理服务器接收请求，再转发给内部网络上的服务器，并将从服务器得到的结果返回给internet上请求连接的客户端。
    
#### 2、负载均衡

    将流量分摊到多个服务器，减轻每台服务器的压力；
    
                                ---> tomcat    由于Nignx和Apche只支持静态页面支持CGI协议1的语言(如PHP),不支持java语言，
         Client   --->   Nginx  ---> tomcat  所以java程序只能通过Tomcat发布服务。
                                ---> tomcat
         
#### 3、虚拟主机

#### 4、动静分离
