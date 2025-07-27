# 安装 openwrt (以 友善r2s为例)
- 建议去[官方网站下载](https://firmware-selector.openwrt.org/)，安全性有保证，其他人编译放网上的容易夹带私货。输入你路由器型号查找，就可以下载，例子中 r2s 选择 ext4 文件系统的镜像，后面扩容根目录方便。尽量不要自己编译系统，编译对网络环境要求高，系统环境依赖项多，没必要耗费不必要的精力去折腾。

- 下载 [balenaEtcher](https://etcher.balena.io/) 烧录软件
- r2s 的存储卡放入读卡器，插上电脑用balenaEtcher烧录刚才下载的系统镜像
- 官方镜像的预设根目录很小，基本安装不了什么软件，需要对其扩容，[教程](https://www.youtube.com/watch?v=g6fEcavnDCo)，使用第二种方式最简单（4:30处开始）

# 安装 v2ray 和透明代理
### 注意事项
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

### 安装软件包
step.1 安装v2ray-core
这个项目提供在线安装和离线安装：[https://github.com/kuoruan/openwrt-v2ray]
另一种方式直接用openwrt官方仓库安装（没有测试过）：
```bash
opkg update
opkg install v2ray-core
```

以下安装的包可能需要安装，待确认，可以全部安装，反正也占用不了多少内存
```bash
opkg install luci-i18n-v2raya-zh-cn
opkg install v2ray-geoip
opkg install v2ray-geosite
opkg install v2raya
# ### iptables是实现透明代理的工具，如果想用v2raya实现透明代理的话必须安装一下包
opkg install iptables-nft
opkg install arptables-nft
opkg install ebtables-nft
opkg install xtables-nft

opkg install ca-bundle
opkg install ip-full
opkg install iptables-mod-conntrack-extra
opkg install iptables-mod-extra
opkg install iptables-mod-filter
opkg install iptables-mod-tproxy
opkg install kmod-ipt-nat6
```

安装好以后进入 v2raya 的 web 管理页面可能还会提示在下载 `geoip.dat` 和 `geosite.dat`文件，在没有国际互联网的情况下这两个文件下载非常缓慢，还容易下载失败，可以 google 搜索下载，然后通过 WinSCP 等方式传入 openwrt 的 /usr/share/v2ray目录下。
进入 v2raya 的 web 管理页面，添加好代理节点后再进入右上角“设置”里，设置一下透明代理。

# 安装 passwall2
* 下载passwall的安装包：[passwall2](https://github.com/xiaorouji/openwrt-passwall2/releases)，选择自己cpu架构对应的安装包
* 解压 .zip 文件，并且上传至openwrt主机。
* cd 至 passwall2 安装包所在的文件夹运行如下命令
  ```bash
  opkg update
  # 透明代理基础依赖
  opkg install ipset  iptables-nft  kmod-nft-socket  kmod-nft-tproxy
  # 安装下载的所有包
  opkg install *.ipk
  # 进入 luci-app-passwall2_xxx_all，luci-i18n-passwall2-zh-cn_xxx_all 所在的文件夹
  opkg install *.ipk
  ```


# openwrt 作为旁路网关使用
- 旁路网关（网上也流传叫旁路由、独臂路由等），使用比较灵活，可以实现分设备代理（需要代理的设备设置网关和 DNS 为 openwrt 的 ip，不需要代理的设备随主路由）。设置 openwrt 为旁路网关方法：
  * 在“网络”-“接口”的“常规设置”中给 Openwrt 的 LAN 网络接口设置一个和现有局域网同网段的静态 IP 地址，注意不要和现有设备的 IP 地址冲突。然后应用设置。
  * 将 LAN 网络接口的“默认网关”设为主路由的 IP 地址。
  * 在“高级设置”中找到“使用自定义的 DNS 服务器”设为主路由的 IP 地址
  * 在“DHCP 服务器”中勾选“忽略此接口”。
  * 在“DHCP 服务器”-“IPv6 设置”中禁用所有IPv6服务。
  * 点击“保存”以及“保存并应用”。
  * 在“网络”-“防火墙”中，关闭“SYN-flood 防御”，点击“保存并应用”。
  * 建议重启一次。

- 然后让 openwrt 的设备上的刚才设置好的 lan 口连接主路由的 lan 口就可以了。
- 需要设置代理的设备手动设置 网关和 dns 为 openwrt 的 ip

# 故障排除
- 遇到忘记密码等问题时候可以取出内存卡，连接电脑手动修改 openwrt 的系统配置文件。
- 网络设置错了导致无法 ssh 连接设备或者打不开 web 页面可以删除 `/etc/config/network`文件，网络设置会重置

