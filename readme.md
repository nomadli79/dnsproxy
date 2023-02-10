[中文](https://github.com/nomadli79/dnsproxy/blob/main/readme_cn.md)

## functions of dnsproxy
- Set private domain name: save the configuration according to the source IP address of the submitted DNS settings. When a DNS request is sent, the IP address corresponding to the domain name is returned based on the source IP address of the request.
- Set a generic private domain name: If no results are found in the first step, the generic private domain name will be queried and the corresponding IP address will be returned.
- DNS forwarding: If the above two steps do not match the results, the DNS request is forwarded to the configured DNS server.
- TLS proxy: If TLS proxy is selected for the configured domain name, the DNS request will return the current DNS server address, convert https to http and send to the corresponding http service.

## install
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

## DNSProxy use help
- Set the DNS server of system to the DNSproxy server address
- Using a browser to access the http://DNSproxy:port, the web page will automatically download or prompt to install the root certificate depending on the operating system
- Using a browser to access the http://DNSproxy:port/list configure DNS
    - If no TLS proxy is selected, the IP address cannot be followed by the port number, and DNSproxy will automatically remove the port number. eg: 1.1.1.1,2.2.2.2
    - If you choose TLS proxy, you need to have a port number after the IP address, if you do not set the port number, DNS Sproxy will automatically add port 80. eg: 1.1.1.1:8080,2.2.2.2:8080
- If it does not take effect after following the above steps, clear the local DNS cache and turn off the network proxy tool. Some network proxy tools ignore system DNS servers.

## Contact
- dzym79@qq.com
