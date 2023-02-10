## dnsproxy 功能
- 设置私有域名: 设置使用域名时,系统会根据设置时的源IP地址进行分类保存,当收到DNS强求后系统会根据源IP地址返回不同的解析结果。因此设备更换IP后设置会失效.
- 设置全局私有域名: 在配置文件设置全局私有域名,系统在DNS请求源IP地址下未找到结果,会查找全局私有域名并返回结果.
- DNS转发: 如果上述两步都未找到结果,系统会转发强求到配置中的上游DNS服务器.
- TLS代理: 如果配置时勾选了TLS代理,则会将https强求转为http请求并将流量代理到配置的IP地址及端口上.

## dnsproxy 服务器安装以linux为例
```shell
systemctl status systemd-journald
systemctl status rsyslog

iptables -I INPUT 1 -p udp --dport 53 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 8080 -j ACCEPT
iptables -I INPUT 1 -p tcp --dport 443 -j ACCEPT
iptables-save > /etc/sysconfig/iptables
systemctl restart iptables

#mkdir -p log and cers path
cp ./dnsproxy.service /usr/lib/systemd/system/
systemctl enable dnsproxy
systemctl start dnsproxy
systemctl status dnsproxy

journalctl -u dnsproxy --since today
journalctl -u dnsproxy --since yesterday --until now
journalctl -u dnsproxy --since "2022-08-13" --until "2022-08-14 03:00"
```

## DNSProxy客户端配置步骤
- 将客户端系统的DNS解析设置为DNSproxy服务的地址,不要保留其它DNS服务器.
- 浏览器访问http://DNSproxy:port, 这个网页会根据不同的操作系统及浏览器提示安装根证书或下载根证书.然后安装根证书并信任证书.如果不需要TLS代理这步可以省略.
- 浏览器访问http://DNSproxy:port/list 这个网页可以配置私有域名
    - 如果没有勾选TLS代理, IP地址后不要设置端口号,提交配置后如果有端口号会自动删除,端口号应该在访问域名时带在域名后. eg: 1.1.1.1,2.2.2.2
    - 如果勾选了TLS代理, IP地址后应该设置端口号,提交配置后如果没有端口号会自动添加80端口,访问域名时不要带端口号. eg: 1.1.1.1:8080,2.2.2.2:8080
- 如果配置后不起作用,应该清空系统DNS缓存,并关闭网络代理程序,部分网络代理程序会绕过系统DNS配置防止DNS污染.

## 联系方式
- dzym79@qq.com
