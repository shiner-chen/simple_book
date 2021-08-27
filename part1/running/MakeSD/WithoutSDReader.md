更新时间：2021/05/07 GMT+08:00

介绍无读卡器场景下，通过跳线帽/杜邦线短接开发者板引脚的方式制作SD卡的操作。

#### 硬件准备

1.  拆卸上盖，请参见[拆卸上盖](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0004.html)。
2.  请准备跳线帽或者杜邦线，然后参考[图1](#ZH-CN_TOPIC_0000001072593087__fig812171232616)短接开发者板上的16号与18号两个针脚。
    
    ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
    须知：
    
    *   请在开发者板断电条件下执行此操作。开发者板的下电操作要求请参考[下电Atlas 200 DK开发者板](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0031.html)。
    *   请仔细核对针脚位置，若针脚插错将会对Atlas 200 DK开发者板造成严重损坏。
    *   面板上用白色字体标出了1、2、40三个针脚的位置，可仔细查看。
    
    **图1** 杜邦线安装示例图  
    ![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001073203414.png)
    
3.  将Atlas 200 DK开发者板与Ubuntu服务器的USB连接。
4.  将Atlas 200 DK开发者板上电，详细上电操作请参考[上电Atlas 200 DK开发者板](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0030.html)。

#### 软件包准备

获取开发者板驱动与运行包、Ubuntu操作系统镜像包，下载信息如[表1](#ZH-CN_TOPIC_0000001072593087__zh-cn_topic_0000001073203404_table6796615269)所示：

**表1** 软件包信息
| 包信息 | 包名 | 说明 | 下载地址 |
| --- | --- | --- | --- |
| Ubuntu操作系统镜像包 | ubuntu-18.04.xx-server-arm64.iso | 开发者板操作系统镜像包。<br>Ubuntu操作系统的版本可以为：18.04.4、18.04.5。<br>**注意：为ARM版本的Server软件包。**  | 请从[华为开源镜像](https://repo.huaweicloud.com/ubuntu-cdimage/releases/)或者[Ubuntu官网](http://releases.ubuntu.com/releases/)下载此软件包。 |
| 开发者板驱动包与运行包 | A200dk-npu-driver-_{software version}_\-ubuntu18.04-aarch64-minirc.tar.gz | 开发者板驱动包，包含OS外围软件、AI软件栈、维测相关软件及驱动。<br>制作SD卡时，会从driver包中获取Firmware信息，不需要单独获取Firmware组件包。 | [获取链接](https://ascend.huawei.com/#/hardware/firmware-drivers)<br>1.  “产品系列”请选择“AI开发者套件”。<br>2.  “产品型号”请选择“Atlas 200 DK开发者套件”。<br>3.  “版本号”：选择“1.0.9.alpha”。 |
| Ascend-cann-nnrt\__{software version}_\_linux-aarch64.run | CANN相关软件包，包含AI CPU算子包与Ascend CL安装包。<br>制卡时使用HwHiAiUser用户进行CANN包的安装，并会自动在HwHiAiUser用户的.bashrc文件中设置LD\_LIBRARY\_PATH与PYTHONPATH的环境变量：<br>**export LD\_LIBRARY\_PATH=/home/HwHiAiUser/Ascend/acllib/lib64**<br>**export PYTHONPATH=/home/HwHiAiUser/Ascend/pyACL/python/site-packages/acl** | [获取链接](https://ascend.huawei.com/#/software/cann/community)<br>选择配套的CANN软件包下载。<br>支持的CANN版本请参见[版本配套说明](https://support.huaweicloud.com/versionmap-Atlas200DK202/atlasversionmap_18_0001.html)。 |

![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
须知：

软件下载后请保持原命名。

#### 操作步骤

1.  在Ubuntu服务器中执行如下命令安装qemu-user-static、binfmt-support、yaml、squashfs-tools与交叉编译器。
    
    **su - root**
    
    执行如下命令更新源：
    
    **apt-get update**
    
    执行如下命令安装相关python依赖：
    
    **pip3 install pyyaml**
    
    **apt-get install qemu-user-static binfmt-support python3-yaml squashfs-tools gcc-aarch64-linux-gnu g++-aarch64-linux-gnu**
    
2.  在Ubuntu服务器中以root用户执行如下命令创建制卡工作目录。
    
    **mkdir /home/ascend/mksd**
    
    制卡目录可任意指定。
    
3.  将[软件包准备](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0012.html#ZH-CN_TOPIC_0000001073203404__section598113515451)获取的Ubuntu操作系统镜像包、开发者板所有驱动包上传到制卡工作目录中（例如：“/home/ascend/mksd”）。
4.  在制卡工作目录下（例如：“/home/ascend/mksd”）执行如下命令获取制卡脚本。
    
    *   下载制卡入口脚本“make\_sd\_card.py”。
        
        从gitee下载：
        
        **wget https://gitee.com/ascend/tools/raw/master/makesd/for\_1.0.9.alpha/make\_sd\_card.py**
        
        从github下载：
        
        **wget https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\_1.0.9.alpha/make\_sd\_card.py**
        
    *   下载制作SD卡操作系统的脚本“make\_ubuntu\_sd.sh”。
        
        从gitee下载：
        
        **wget https://gitee.com/ascend/tools/raw/master/makesd/for\_1.0.9.alpha/make\_ubuntu\_sd.sh**
        
        从github下载：
        
        **wget** **https://raw.githubusercontent.com/Ascend/tools/master/makesd/for\_1.0.9.alpha/make\_ubuntu\_sd.sh**
        
    
    ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
    说明：
    
    您可以通过修改“make\_sd\_card.py”中的如下参数配置Atlas 200 DK的USB网卡IP与NIC网卡IP。
    
    *   “NETWORK\_CARD\_DEFAULT\_IP”：NIC网卡的IP地址，默认值“192.168.0.2”。
    *   “USB\_CARD\_DEFAULT\_IP”：USB网卡的IP地址，默认值“192.168.1.2”。
    
5.  执行制卡脚本。
    1.  以root用户执行如下命令查找SD卡所在的USB设备名称。
        
        **fdisk -l**
        
        例如，SD卡所在USB设备名称为“/dev/sda”，可通过插拔SD卡的方式确定设备名称。
        
    2.  运行SD制卡脚本“make\_sd\_card.py”。
        
        **python3 make\_sd\_card.py local /dev/sda**
        
        *   “local”表示使用本地方式制作SD卡。
        *   “/dev/sda”为SD卡所在的USB设备名称。
        
        如[图2](#ZH-CN_TOPIC_0000001072593087__zh-cn_topic_0000001073203404_fig20652165614402)所示表示制卡成功。
        
        **图2** SD制卡回显信息示例  
        ![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001073043508.png)
        
        ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
        说明：
        
        如果制卡失败，可以查看当前目录下的sd\_card\_making\_log文件夹下的日志文件进行分析。
    
6.  制卡成功后，将Atlas 200 DK开发者板下电，详细下电操作请参考[下电Atlas 200 DK开发者板](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0031.html)。
7.  拔掉跳线帽或者杜邦线。
8.  上电Atlas 200 DK开发者板。
    
    ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
    须知：
    
    *   Atlas 200 DK制卡后，首次上电启动过程中会进行固件的升级，升级完成后会自动进行重启的操作，重启后再进行其他组件的安装。
    *   首次启动Atlas 200 DK开发者板时不能断电，以免对Atlas 200 DK开发者板造成损害，再次上电需与上次下电时间保持2S以上的安全时间间隔。
    
    Atlas 200 DK开发者板的上电操作及上电后指示灯的状态说明，请参见[上电Atlas 200 DK开发者板](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0030.html)。
    

#### 异常处理

若上电后，开发者板无法正常启动（例如，指示灯状态异常），可通过如下步骤查看相关日志：

1.  将Atlas 200 DK下电。
2.  使用跳线帽/杜邦线短接Atlas 200 DK的16与18号针脚，如[硬件准备](#ZH-CN_TOPIC_0000001072593087__section1323185865511)所示，将Atlas 200 DK作为USB设备。
3.  将Atlas 200 DK通过USB端口与Ubuntu服务器链接，并上电Atlas 200 DK。
4.  以root用户执行如下命令查找SD卡所在的USB设备及其分区信息。
    
    **fdisk -l**
    
    显示信息如下所示：
    
    ```
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
    ```
    
5.  将SD卡的第一个分区挂载到Ubuntu服务器。
    1.  使用root用户，创建一个空目录。
        
        例如：
        
        **mkdir -p _/home/sdinfo_**
        
    2.  将/dev/sda1挂载到/home/sdinfo目录。
        
        **mount /dev/sda1 _/home/sdinfo_**
    
6.  进入/home/sdinfo目录，即进入Atlas 200 DK的文件系统，可在此目录的var/log/ascend\_seclog路径下查看相关日志文件。
    
    **cd _/home/sdinfo_**
    
    **cd var/log/ascend\_seclog/**
    
    可查看此路径的日志文件，日志文件描述如下所示：
    
    *   operation.log：操作日志，记录安装或升级等事件的结果信息，通过此日志可看出安装升级的最终结果是成功还是失败。
        
        日志格式：事件类型+事件级别+用户ID+日期+发起端地址+访问文件名+操作指令+执行结果
        
    *   ascend\_install.log：安装/升级详细日志，记录安装/升级的详细运维脚本日志，可通过此日志查看安装/升级的详细操作及状态信息。
        
        日志格式：组件+日期+日志级别+日志内容
        
    *   ascend\_run\_servers.log：Atlas 200 DK启动信息。
    
    ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
    须知：
    
    *   若开发者无法解决问题，在[Ascend论坛](https://bbs.huaweicloud.com/forum/forum-726-1.html)上发求助帖，并将以上日志信息作为帖子的附件上传，将会有华为工程师为您解答。
    *   若Atlas 200 DK启动三次及以上后均失败，解决问题后，需要删除**var/log/ascend\_seclog/**目录下的boot\_fail\_count文件，否则Atlas 200 DK无法正常启动。
    

### 意见反馈

### 文档是否有解决您的问题？

提交成功！非常感谢您的反馈，我们会继续努力做到更好！