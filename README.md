# armbian-clash
recode proxy by clash
Clash Dashboard
可选安装，我觉得用处不大

web端控制台，新手向，相关设置一目了然

引用站外链接
Dashboard
Github
在本地下载并构建，上传至/opt/clash/ui

后续设置的secret将用于登录


Clash控制面板

Clash Premium
Premium版Clash，可实现TUN模式

引用站外链接
Clash Premium
Github
Bash
wget -O clash.gz https://github.com/Dreamacro/clash/releases/download/premium/clash-linux-amd64-2023.08.17.gz
Bash
gzip -dc clash.gz > /usr/local/bin/clash
chmod +x /usr/local/bin/clash
Country.mmdb
提高网络分流的效率和质量

引用站外链接
GeoIP
Github
Bash
mkdir /etc/clash
wget -O /etc/clash/Country.mmdb https://github.com/Dreamacro/maxmind-geoip/releases/download/20230812/Country.mmdb
clash.service
创建clash对应的systemd脚本，实现开机自启，持久化运行

Bash
vim /etc/systemd/system/clash.service

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
external-ui: /opt/clash/ui

secret: "******"

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
测试

启动Clash服务


Ping Google

作者: btwoa
链接: https://blog.btwoa.com/53da714d/
来源: 放养平凡
著作权归作者所有。 商业转载请联系作者获得授权，非商业转载请注明出处。
