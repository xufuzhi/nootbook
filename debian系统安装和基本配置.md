## 网络配置
  #### 关闭 dhcpcd 服务（防止静态设置被覆盖），通过锁定 `/etc/resolv.conf` 文件达到关闭 dhcpcd 的效果
  1. 编辑 /etc/resolv.conf：
      ```bash
      sudo nano /etc/resolv.conf
      ```
  2. 删除或注释掉其他所有内容（包括旧的 nameserver 和注释行）。写入你期望的静态 DNS 服务器，比如：
      ```bash
      nameserver 192.168.1.99
      nameserver 8.8.8.8
      ```
  3. 保存并退出
  4. 检查锁定状态：
      ```bash
      lsattr /etc/resolv.conf
      ```
      输出中应该包含 i 标志，例如：----i---------e------- /etc/resolv.conf

  5. 重启系统 
  6. 重启后验证 DNS：
      ```bash
      cat /etc/resolv.conf
      ```
      内容应该仍然是 192.168.1.99 和 8.8.8.8。如果内容正确，那么你的 DNS 问题就永久解决了。

   注意： 一旦锁定，如果你以后需要更改 DNS 服务器，必须先运行 sudo chattr -i /etc/resolv.conf 解锁文件才能进行编辑
