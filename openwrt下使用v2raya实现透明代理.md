# 注意事项
- 如果openwrt这台路由器是连接另一台路由器的话一定要避免两台路由处于同一网段，如，上一级路由的网段：`192.168.1.XXX` ，openwrt的网段可以设置成 `192.168.2.xxx` 。因为如果两台路由处于同一网段，那么两台路由器dns和网关就相同，会导致设备上网不稳定。透明代理也是通过网关来实现的，透明代理也不会成功。
- 尽量使用ssh连接路由器用opkg命令安装软件，成功率高
- 路由器没有留学能力安装软件很慢而且很难成功，可以用一台电脑连接openwrt路由器，在电脑上打开v2rayN，并打开局域网分享功能。然后为opkg设置代理，编辑`/etc/opkg.conf`文件，添加例如`option http_proxy http://192.168.2.xxx:port`信息。为wget设置代理：在用户目录下创建`.wgetrc`文件并且添加信息
  ```bash
  https_proxy = http://192.168.2.x:xxxx/
  http_proxy = http://192.168.2.x:xxxx/
  ftp_proxy = http://192.168.2.x:xxxx/
  
  # If you do not want to use proxy at all, set this to off.
  use_proxy = on
  ```
  提示：可以使用winSCP软件连接openwrt修改和添加文件，相当方便

# 安装软件包
step.1 安装v2ray-core
这个项目提供在线安装和离线安装：[https://github.com/kuoruan/openwrt-v2ray]
另一种方式直接用openwrt官方仓库安装（没有测试过）：
```bash
opkg update
opkg install v2ray-core
```
