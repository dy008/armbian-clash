# armbian-clash
recode proxy by clash

Clash Dashboard
可选安装，我觉得用处不大

web端控制台，新手向，相关设置一目了然


在本地下载并构建，上传至/opt/clash/ui

Clash控制面板

Clash Premium
Premium版Clash，可实现TUN模式

Bash
wget -O clash https://github.com/dy008/armbian-clash/releases/download/backup/clash-linux-arm64-n2023-09-05-gdcc8d87

Bash
cp clash /usr/local/bin/clashp
chmod +x /usr/local/bin/clashp

Bash
mkdir /etc/clash

clash.service
创建clash对应的systemd脚本，实现开机自启，持久化运行

Bash
nano /etc/systemd/system/clash.service

Code
[Unit]
Description=Clash 守护进程, Go 语言实现的基于规则的代理.
After=network.target NetworkManager.service systemd-networkd.service iwd.service

[Service]
Type=simple
LimitNPROC=500
LimitNOFILE=1000000
Restart=always
ExecStartPre=/usr/bin/sleep 3s
ExecStart=/usr/local/bin/clashp -d /etc/clash
ExecReload=/bin/kill -HUP $MAINPID

[Install]
WantedBy=multi-user.target


重载 systemd

Bash
systemctl daemon-reload

config.yaml
根据机场订阅链接下载

Bash
wget -O /etc/clash/config.yaml https://XXXXX/config.yaml
自定义修改

Yaml
port: 7890
socks-port: 7891

allow-lan: true

mode: rule

external-controller: :9090
external-ui: /etc/clash/ui

secret: ""

dns:
  enable: true
  listen: :53
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.1/16
  fake-ip-filter:
    - '*.btwoa.com'
  nameserver:
    - 114.114.114.114 
    - 8.8.8.8 
    - tls://dns.rubyfish.cn:853 
    - https://1.1.1.1/dns-query 
    - dhcp://en0 
  fallback:
    - 8.8.8.8
tun:
  enable: true
  stack: system
  dns-hijack:
    - 8.8.8.8:53
    - tcp://8.8.8.8:53
    - any:53
    - tcp://any:53
  auto-route: true
  auto-detect-interface: true
.bashrc
Bash
vim ~/.bashrc
Bash
export http_proxy="127.0.0.1:7890"
export https_proxy="127.0.0.1:7891"
export all_proxy="socks5://127.0.0.1:7891"
重载

Bash
source ~/.bashrc
TUN模式
/etc/sysctl.conf
开启转发功能

Bash
vim /etc/sysctl.conf
Conf
net.ipv4.ip_forward=1
重载

Bash
sysctl -p
运行并自启
Bash
systemctl start clash
systemctl enable clash

检查状态
systemctl status clash

测试
curl -I https://google.com

done

