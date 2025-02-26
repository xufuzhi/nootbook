# 注意事项
- openwrt自带`vim`，对`vim`不熟悉的话可以安装`nano`，这个比较人性化，适合偶尔用下的同学。如果使用的是Windows系统，可以安装WinSCP软件连接openwrt修改和添加文件，相当方便
- 如果openwrt这台路由器是连接另一台路由器的话一定要避免两台路由处于同一网段，如，上一级路由的网段：`192.168.1.XXX` ，openwrt的网段可以设置成 `192.168.2.xxx` 。因为如果两台路由处于同一网段，那么两台路由器dns和网关就相同，会导致设备上网不稳定。透明代理也是通过网关来实现的，透明代理也不会成功。修改openwrt的Lan口ip方法如下：
  用vim修改`/etc/config/network`文件
  ```bash
  vim /etc/config/network
  ```
  找到`config interface 'lan'`这个条目，然后按i进入vim的编辑模式修改`option ipaddr '192.168.2.1'`这行的ip为自己想要的段
  ```bash
  config interface 'lan'
        	option device 'br-lan'
        	option proto 'static'
        	option ipaddr '192.168.2.1'
        	option netmask '255.255.255.0'
        	option ip6assign '60'
  ```
  然后按ESC键退出到 vim 的命令模式（可能需要多按几次），输入`:wq`保存退出
  在openwrt的管理页面上也可以修改IP，但是由于页面功能做的很不好，容易失败，不建议使用。
- 尽量使用ssh连接路由器用opkg命令安装软件，成功率高
- 路由器没有留学能力安装软件很慢而且很难成功，可以用一台电脑连接openwrt路由器，在电脑上打开v2rayN，并打开局域网分享功能。然后为opkg设置代理，编辑`/etc/opkg.conf`文件，添加例如`option http_proxy http://192.168.2.xxx:port`信息。为wget设置代理：在用户目录下创建`.wgetrc`文件并且添加信息
  ```bash
  https_proxy = http://192.168.2.x:xxxx/
  http_proxy = http://192.168.2.x:xxxx/
  ftp_proxy = http://192.168.2.x:xxxx/
  
  # If you do not want to use proxy at all, set this to off.
  use_proxy = on
  ```

# 安装软件包
step.1 安装v2ray-core
这个项目提供在线安装和离线安装：[https://github.com/kuoruan/openwrt-v2ray]
另一种方式直接用openwrt官方仓库安装（没有测试过）：
```bash
opkg update
opkg install v2ray-core
```



### 草稿
可能需要安装的包如下，待确认，可以全部安装，反正也占用不了多少内存
```bash
luci-i18n-v2raya-zh-cn
v2ray-geoip
v2ray-geosite
v2raya
# ### iptables是实现透明代理的工具，如果想用v2raya实现透明代理的话必须安装一下包
iptables-nft
arptables-nft
ebtables-nft
xtables-nft

ca-bundle
ip-full
iptables-mod-conntrack-extra
iptables-mod-extra
iptables-mod-filter
iptables-mod-tproxy
kmod-ipt-nat6
```
