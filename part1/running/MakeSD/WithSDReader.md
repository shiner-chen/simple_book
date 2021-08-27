# DLAP221_读卡器场景
更新时间：2021/05/07 GMT+08:00

介绍有读卡器的场景下将读卡器与 Ubuntu 服务器的 USB 连接，通过制卡脚本制作 SD 卡的操作。

#### 硬件准备

SD 卡：准备一张 SD 卡，建议容量不低于 16G。

![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
说明：

制作过程将会对卡做格式化处理，请提前备份数据。

#### 软件包准备

获取开发者板驱动与运行包、Ubuntu 操作系统镜像包，下载信息如[表 1](#ZH-CN_TOPIC_0000001073203404__table6796615269)所示：

**表 1** 软件包信息

|包信息 |   包名   | 说明     | 下载地址|
| ---- | ---- | ---- | ---- |
|Ubuntu 操作系统镜像包| ubuntu-18.04.xx-server-arm64.iso | 开发者板操作系统镜像包。<br>Ubuntu 操作系统的版本可以为：18.04.4、18.04.5。<br>**注意：为 ARM 版本的 Server 软件包。**  | 请从[华为开源镜像](https://repo.huaweicloud.com/ubuntu-cdimage/releases/)或者[Ubuntu 官网](http://releases.ubuntu.com/releases/)下载此软件包。 |
| 开发者板驱动包与运行包 | A200dk-npu-driver-_{software version}_-ubuntu18.04-aarch64-minirc.tar.gz| 开发者板驱动包，包含 OS 外围软件、AI 软件栈、维测相关软件及驱动。制作 SD 卡时，会从 driver 包中获取 Firmware 信息，不需要单独获取 Firmware 组件包。 | [获取链接](https://ascend.huawei.com/#/hardware/firmware-drivers)<br>1.  “产品系列” 请选择 “AI 开发者套件”。<br>2.  “产品型号” 请选择 “Atlas 200 DK 开发者套件”。<br>3.  “版本号”：选择 “1.0.9.alpha”。<br>     |
||Ascend-cann-nnrt\__{software version}_\_linux-aarch64.run | CANN 相关软件包，包含 AI CPU 算子包与 Ascend CL 安装包。<br>制卡时使用 HwHiAiUser 用户进行 CANN 包的安装，并会自动在 HwHiAiUser 用户的. bashrc 文件中设置 LD_LIBRARY_PATH 与 PYTHONPATH 的环境变量：<br>**export LD_LIBRARY_PATH=/home/HwHiAiUser/Ascend/acllib/lib64**<br>**export PYTHONPATH=/home/HwHiAiUser/Ascend/pyACL/python/site-packages/acl**| [获取链接](https://ascend.huawei.com/#/software/cann/community)<br>选择配套的 CANN 软件包下载。<br>支持的 CANN 版本请参见[版本配套说明](https://support.huaweicloud.com/versionmap-Atlas200DK202/atlasversionmap_18_0001.html)。 |

![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
须知：

软件下载后请保持原命名。

#### 操作步骤

1.  请将 SD 卡放入读卡器，并将读卡器与 Ubuntu 服务器的 USB 接口连接。
2.  在 Ubuntu 服务器中执行如下命令安装 qemu-user-static、binfmt-support、yaml、squashfs-tools 与交叉编译器。

    **su - root**

    执行如下命令更新源：

    **apt-get update**

    执行如下命令安装相关 python 依赖：

    **pip3 install pyyaml**

    **apt-get install qemu-user-static binfmt-support python3-yaml squashfs-tools gcc-aarch64-linux-gnu g++-aarch64-linux-gnu**
3.  在 Ubuntu 服务器中以 root 用户执行如下命令创建制卡工作目录。

    **mkdir /home/ascend/mksd**

    制卡目录可任意指定。
4.  将[软件包准备](#ZH-CN_TOPIC_0000001073203404__section598113515451)获取的 Ubuntu 操作系统镜像包、开发者板所有驱动包上传到制卡工作目录中（例如：“/home/ascend/mksd”）。
5.  在制卡工作目录下（例如：“/home/ascend/mksd”）执行如下命令获取制卡脚本。

    -   下载制卡入口脚本 “make_sd_card.py”。

        从 gitee 下载：

        **wget [https://gitee.com/ascend/tools/raw/master/makesd/for\\\_1.0.9.alpha/make\\\_sd\\\_card.py](https://gitee.com/ascend/tools/raw/master/makesd/for\_1.0.9.alpha/make\_sd\_card.py)**

        从 github 下载：

        **wget [https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\\\_1.0.9.alpha/make\\\_sd\\\_card.py](https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\_1.0.9.alpha/make\_sd\_card.py)**
    -   下载制作 SD 卡操作系统的脚本 “make_ubuntu_sd.sh”。

        从 gitee 下载：

        **wget [https://gitee.com/ascend/tools/raw/master/makesd/for\\\_1.0.9.alpha/make\\\_ubuntu\\\_sd.sh](https://gitee.com/ascend/tools/raw/master/makesd/for\_1.0.9.alpha/make\_ubuntu\_sd.sh)**

        从 github 下载：

        **wget** **[https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\\\_1.0.9.alpha/make\\\_ubuntu\\\_sd.sh](https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\_1.0.9.alpha/make\_ubuntu\_sd.sh)**

    ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
    说明：

    您可以通过修改 “make_sd_card.py” 中的如下参数配置 Atlas 200 DK 的 USB 网卡 IP 与 NIC 网卡 IP。

    -   “NETWORK_CARD_DEFAULT_IP”：NIC 网卡的 IP 地址，默认值 “192.168.0.2”。
    -   “USB_CARD_DEFAULT_IP”：USB 网卡的 IP 地址，默认值 “192.168.1.2”。
6.  执行制卡脚本。
    1.  以 root 用户执行如下命令查找 SD 卡所在的 USB 设备名称。

        **fdisk -l**

        例如，SD 卡所在 USB 设备名称为 “/dev/sda”，可通过插拔 SD 卡的方式确定设备名称。
    2.  运行 SD 制卡脚本 “make_sd_card.py”。

        **python3 make_sd_card.py local /dev/sda**

        -   “local” 表示使用本地方式制作 SD 卡。
        -   “/dev/sda” 为 SD 卡所在的 USB 设备名称。

        如[图 1](#ZH-CN_TOPIC_0000001073203404__fig20652165614402)所示表示制卡成功。

        **图 1** SD 制卡回显信息示例  
        ![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001073043508.png)

        ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
        说明：

        如果制卡失败，可以查看当前目录下的 sd_card_making_log 文件夹下的日志文件进行分析。
7.  制卡成功后，将 SD 卡从读卡器取出并插入 Atlas 200 DK 开发者板卡槽。
8.  上电 Atlas 200 DK 开发者板。

    ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
    须知：

    -   Atlas 200 DK 制卡后，首次上电启动过程中会进行固件的升级，升级完成后会自动进行重启的操作，重启后再进行其他组件的安装。
    -   首次启动 Atlas 200 DK 开发者板时不能断电，以免对 Atlas 200 DK 开发者板造成损害，再次上电需与上次下电时间保持 2S 以上的安全时间间隔。

    Atlas 200 DK 开发者板的上电操作及上电后指示灯的状态说明，请参见[上电 Atlas 200 DK 开发者板](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0030.html)。

#### 异常处理

若上电后，开发者板无法正常启动（指示灯状态异常），可通过如下步骤查看相关日志：

1.  将 Atlas 200 DK 下电。
2.  从 Atlas 200 DK 中拔出 SD 卡，将 SD 卡插入读卡器，并将读卡器与 Ubuntu 服务器的 USB 端口连接。
3.  以 root 用户执行如下命令查找 SD 卡所在的 USB 设备及其分区信息。

    **fdisk -l**

    显示信息如下所示：

        Disk /dev/sda: 29.7 GiB, 31914983424 bytes, 62333952 sectors
        Units: sectors of 1 \* 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disklabel type: dos
        Disk identifier: 0x00000000
        
        Device     Boot    Start      End  Sectors  Size Id Type
        /dev/sda1           2048 10487807 10485760    5G 83 Linux
        /dev/sda2       10487808 12584959  2097152    1G 83 Linux
        /dev/sda3       12584960 62333951 49748992 23.7G 83 Linux
4.  将 SD 卡的第一个分区挂载到 Ubuntu 服务器。
    1.  使用 root 用户，创建一个空目录。

        例如：

        **mkdir -p _/home/sdinfo_**
    2.  将 / dev/sda1 挂载到 / home/sdinfo 目录。

        **mount /dev/sda1 _/home/sdinfo_**
5.  进入 / home/sdinfo 目录，即进入 Atlas 200 DK 的文件系统，可在此目录的 var/log/ascend_seclog 路径下查看相关日志文件。

    **cd _/home/sdinfo_**

    **cd var/log/ascend_seclog/**

    可查看此路径的日志文件，日志文件描述如下所示：

    -   operation.log：操作日志，记录安装或升级等事件的结果信息，通过此日志可看出安装升级的最终结果是成功还是失败。

        日志格式：事件类型 + 事件级别 + 用户 ID + 日期 + 发起端地址 + 访问文件名 + 操作指令 + 执行结果
    -   ascend_install.log：安装 / 升级详细日志，记录安装 / 升级的详细运维脚本日志，可通过此日志查看安装 / 升级的详细操作及状态信息。

        日志格式：组件 + 日期 + 日志级别 + 日志内容
    -   ascend_run_servers.log：Atlas 200 DK 启动信息。

    ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
    须知：

    -   若开发者无法解决问题，在[Ascend 论坛](https://bbs.huaweicloud.com/forum/forum-726-1.html)上发求助帖，并将以上日志信息作为帖子的附件上传，将会有华为工程师为您解答。
    -   若 Atlas 200 DK 启动三次及以上后均失败，解决问题后，需要删除**var/log/ascend_seclog/**目录下的 boot_fail_count 文件，否则 Atlas 200 DK 无法正常启动。

### 意见反馈

### 文档是否有解决您的问题？

提交成功！非常感谢您的反馈，我们会继续努力做到更好！ 
 [https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0012.html](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0012.html)
