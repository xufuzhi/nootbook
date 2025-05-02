# 1. 下载源码
- opencv源码： [https://github.com/opencv/opencv/releases](https://github.com/opencv/opencv/releases)
- 要编译带cuda支持的opencv还需要下载opencv_contrib，[https://github.com/opencv/opencv_contrib/tags](https://github.com/opencv/opencv_contrib/tags)，注意opencv_contrib的版本要和opencv的版本对应

# 2. 安装cuda和cudnn
- 卸载cuda，去软件卸载界面找到除了显卡驱动之外所有名字带`nvidia`、`cuda`或`cudnn`的程序，全部卸载，安装cuda时候会跟随安装很多插件，所有一定要全部卸载了，除非你明确知道那个软件和`cuda`没有关系.
- 在这里[https://developer.nvidia.com/cuda-toolkit](https://developer.nvidia.com/cuda-toolkit)下载本地安装包（在线安装容易失败）。小技巧：选择正确的版本后关闭科学上网工具，然后再点击下载，速度可以很快（cuda下载地址没有被墙）
- 点击安装`cuda`，选择自定义安装，确保`visual studio integration`选项是勾选状态。

![tu](https://github.com/xufuzhi/nootbook/blob/images/all_images/1.png)


- 点击下一步继续安装，选择安装位置
