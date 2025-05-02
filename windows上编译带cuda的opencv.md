# 注意事项
- cuda toolkit 和 visual studio 的版本有适配要求，如果版本不适配在cmake的时候就会报错找不到cuda，建议cuda的版本发布时间要在 vs 的版本发布时间之后，如果有问题多换几个版本试试
- 编译过程中会在线下载文件，保持国际网络畅通

# 1. 安装cuda和cudnn
### 1.1 安装cuda
- 卸载cuda，去软件卸载界面找到除了显卡驱动之外所有名字带`nvidia`、`cuda`或`cudnn`的程序，全部卸载，安装cuda时候会跟随安装很多插件，所有一定要全部卸载了，除非你明确知道那个软件和`cuda`没有关系.
- 在这里[https://developer.nvidia.com/cuda-toolkit](https://developer.nvidia.com/cuda-toolkit)下载本地安装包（在线安装容易失败）。小技巧：选择正确的版本后关闭科学上网工具，然后再点击下载，速度可以很快（cuda下载地址没有被墙）
- 点击安装`cuda`，选择自定义安装，确保`visual studio integration`选项是勾选状态。

    ![tu](https://github.com/xufuzhi/nootbook/blob/images/all_images/1.png)


- 点击下一步继续安装，选择安装位置， 等待完成安装
- 打开cmd终端，输入命令 `nvcc -V` 验证安装是否成功。
### 1.2 安装 cudnn
- 建议使用压缩文件格式手动安装，[https://developer.download.nvidia.com/compute/cudnn/redist/cudnn/windows-x86_64/](https://developer.download.nvidia.com/compute/cudnn/redist/cudnn/windows-x86_64/)，注意存档名字和cuda版本匹配。（如果想从.exe格式安装，google搜索cudnn下载，双击安装，不过强烈不建议，当前版本安装上需要手动配置path）
- 解压cudnn安装包，把里面 `bin`, `include`, `lib` 三个文件夹复制到 `cuda` 的安装文件夹下，直接粘贴就行，有同名的文件夹会自动合并。

# 2. 编译并安装 OpenCV
- 创建一个文件夹，例如： `opencv_build`，下载opencv源码： [https://github.com/opencv/opencv/releases](https://github.com/opencv/opencv/releases) 到此文件夹。要编译带cuda支持的opencv还需要下载opencv_contrib，[https://github.com/opencv/opencv_contrib/tags](https://github.com/opencv/opencv_contrib/tags)，注意opencv_contrib的版本要和opencv的版本对应
- 解压两个源码文件，创建一个工作文件夹：`build`
- 打开 cmake， 选择opencv源码位置和编译工作目录位置
    ![](https://github.com/xufuzhi/nootbook/blob/images/all_images/2.png)

- 点击 `Configure` 按钮，选择vs编译器版本和平台（这里选了x64），点击`finish`，等待进度条
    ![](https://github.com/xufuzhi/nootbook/blob/images/all_images/3.png)

- 进度条完成后在搜索框输入 `extra` ，下面编辑框出现配置项，地址换成`opencv_contrib-xxx/modules`的位置，这样opencv就能找到contrib模块位置了

    ![](https://github.com/xufuzhi/nootbook/blob/images/all_images/4.png)
- 继续在搜索框输入 `CUDA` ，下面编辑框里带 CUDA 的选项全部勾选。
- 继续在搜索框输入 `install`, `CMAKE_INSTALL_PREFIX` 是编译好的opencv安装文件夹位置，选一个你喜欢的位置，编译完成会生成那个文件夹，默认是在cmake工作目录下的 `install` 文件夹，使用默认位置，在编译完成后手动copy install 文件夹到喜欢的位置也可以
- 第二次点击 `Configure` 按钮，等待进度条完成后看看有没有报错，如果有报错先解决报错问题然后继续点击 `Configure`，构建成功
- 点击 `Generate` 按钮生成最终编译材料
- 生成完成后点击 `Open Project` 按钮，这时会自动打开visual studio，在visual studio工具栏选择编译类型（`debug`或`release`），还有平台（ x64 \ win32等）
- 点击 `CMake Targets` 选项卡，在里边的 `ALL_BUILD` 选项上右击，选择“生成”，然后进入漫长的编译过程。
- 编译完成后继续在`CMake Targets` 选项卡下面的右击 `INSTALL`,选择“生成”，将会生成一个安装文件夹（位置在上面设置`CMAKE_INSTALL_PREFIX`的地方）
- 打开环境变量配置窗口，确认有 `OpenCV_DIR` 变量，并且正确指向opencv安装文件夹位置

    ![](https://github.com/xufuzhi/nootbook/blob/images/all_images/5.png)
