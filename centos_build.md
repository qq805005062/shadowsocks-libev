### 1、准备编译环境
#### CentOS 7
```
yum install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel libsodium-devel mbedtls-devel -y
```
#### CentOS 8
```
yum install gcc gettext autoconf libtool automake make pcre-devel asciidoc xmlto c-ares-devel libev-devel -y
```
### 2、获取shadowsocks-libev源码, 并安装
```
yum install git
git clone https://github.com/shadowsocks/shadowsocks-libev.git
cd shadowsocks-libev
git submodule update --init
./autogen.sh && ./configure --disable-documentation && make
sudo make install
```
如果出现 error: The Sodium crypto library libraries not found 错误，安装 libsodium

如果出现 error: mbed TLS libraries not found. 错误，安装 mbedtls

### 3、 创建配置文件
```
sudo mkdir /etc/shadowsocks-libev
sudo vi /etc/shadowsocks-libev/config.json
```
复制粘贴如下内容（注意修改密码“password”）：
```
{
     "server":"0.0.0.0",
     "server_port":8388,
     "local_port":1080,
     "password":"password",
     "timeout":600,
     "method":"aes-256-cfb" ,
     "fast_open": false
 }
 ```
### 4、创建Shadowsocks-libev.service配置文件
sudo vi /etc/systemd/system/shadowsocks-libev.service
复制粘贴：
```
[Unit]
Description=Shadowsocks-libev Server
Documentation=https://shadowsocks.org/en/
After=network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/ss-server -c /etc/shadowsocks-libev/config.json -u
Restart=on-abort

[Install]
WantedBy=multi-user.target
```
### 5、启动 Shadowsocks-libev 服务
```
sudo systemctl start shadowsocks-libev
```
### 6、设置开机启动
```
sudo systemctl enable shadowsocks-libev
```
### 至此， Shadowsocks-libev服务器端的基本配置已经全部完成了！

### 设置防火墙开放端口
### 开放 tcp/udp 8388 端口
```
firewall-cmd --zone=public --add-port=8388/tcp --permanent
firewall-cmd --zone=public --add-port=8388/udp --permanent
```
### 重新载入防火墙配置，使规则生效
```
sudo systemctl restart firewalld
```
