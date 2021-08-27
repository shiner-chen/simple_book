# DLAP221_连接Atlas 200 DK开发者板与Ubuntu服务器
#### 场景描述

Atlas 200 DK 开发者板支持通过 USB 端口或者网线与 Ubuntu 服务器进行连接，连接示例图如[图 1](#ZH-CN_TOPIC_0000001072723391__fig179514213618)所示。

**图 1** Atlas 200 DK 与 MindStudio 连接图  
![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001072935417.png)

Atlas 200 DK 连接 Ubuntu 服务器有以下场景：

-   [通过 USB 端口直连 Ubuntu 服务器](#ZH-CN_TOPIC_0000001072723391__section16361183832019)：使用 USB 连接线通过 USB 端口与 Ubuntu 服务器直连。

    此种场景下，Atlas 200 DK 不方便接入网络，仅适用于与 Ubuntu 服务器进行通信。
-   [使用网线直连 Ubuntu 服务器](#ZH-CN_TOPIC_0000001072723391__section3353164513183)：使用网线将 Atlas 200 DK 与 Ubuntu 服务器的网口直连。

    此种场景下，Atlas 200 DK 不方便接入网络，仅适用于与 Ubuntu 服务器进行通信。
-   [（推荐）使用网线通过路由器连接 Ubuntu 服务器](#ZH-CN_TOPIC_0000001072723391__section26591013141910)：使用网线将 Atlas 200 DK 接入路由器的网口，通过网络与 Ubuntu 服务器连接。

    **推荐使用此种方式**，此种方式下，Atlas 200 DK 可直接接入网络。

#### 通过 USB 端口直连 Ubuntu 服务器

Atlas 200 DK 开发者板与 Ubuntu 服务器通过 USB 端口直连场景下，由于 Atlas 200 DK 的 USB 网卡的默认 IP 地址为 192.168.1.2，所以需要修改 Ubuntu 服务器的 USB 虚拟网卡的 IP 地址为 192.168.1.x（x 取值范围为 0~1，3~254），使 Atlas 200 DK 与 Ubuntu 服务器可以通信。

![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
须知：

-   若制作 SD 卡时，您已修改 Atlas 200 DK 开发者板的 USB 网卡地址与 Ubuntu 服务器的虚拟 USB 网卡的 IP 地址在同一网段，则以下操作可直接跳过。
-   若您的 Ubuntu 服务器是通过 Windows 主机上的虚拟机进行安装的场景，需要参考[如何安装 windows 的 USB 网卡驱动](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0037.html)在 Windows 主机上安装 USB 虚拟网卡驱动，否则 Ubuntu 服务器无法识别 Atlas 200 DK 的 USB 虚拟网卡。

下面提供通过脚本配置 Ubuntu 服务器的 USB 虚拟网卡的 IP 地址与手工配置 Ubuntu 服务器 USB 虚拟网卡 IP 地址的两种方法。

假设您已经通过 USB 连接线将 Atlas 200 DK 与 Ubuntu 服务器的 USB 端口进行连接，操作步骤如下：

-   通过脚本配置
    1.  执行如下命令下载 configure_usb_ethernet.sh 脚本，并将其上传到 Ubuntu 服务器的任一目录，例如_/home/ascend/config_usb_ip/_。

        从 gitee 下载：

        **wget [https://gitee.com/ascend/tools/raw/master/configure\\\_usb\\\_ethernet/for\\\_20.1/configure\\\_usb\\\_ethernet.sh](https://gitee.com/ascend/tools/raw/master/configure\_usb\_ethernet/for\_20.1/configure\_usb\_ethernet.sh)**

        从 github 下载：

        **wget [https://raw.githubusercontent.com/Huawei-Ascend/tools/master/configure\\\_usb\\\_ethernet/for\\\_20.1/configure\\\_usb\\\_ethernet.sh](https://raw.githubusercontent.com/Huawei-Ascend/tools/master/configure\_usb\_ethernet/for\_20.1/configure\_usb\_ethernet.sh)**

        ![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
        须知：

        通过脚本配置仅针对首次配置 USB 网卡对应 IP 地址的场景。USB 网卡 IP 已经配置，若需要修改其 IP 地址，请参考 [手工配置](#ZH-CN_TOPIC_0000001072723391__li13414184332513)修改 USB 网卡的 IP 地址。
    2.  以 root 用户进入配置 USB 网卡 IP 地址的脚本所在目录，例如_/home/ascend/config_usb_ip_。
    3.  执行如下命令进行 USB 网卡 IP 地址的配置。

        **bash configure_usb_ethernet.sh -s** _ip_address_

        以指定的 IP 地址配置 Ubuntu 服务器中 USB 网卡的静态 IP 地址，如果直接执行**bash configure_usb_ethernet.sh**，则以默认 IP 地址 “192.168.1.166” 进行配置。

        -   如果存在多个 USB 网卡，则首先执行**ifconfig**命令查询 USB 网卡名称（若系统中有多个 USB 网卡，可以通过拔插开发者板进行判定，Ubuntu 服务器会将 Atlas 200 DK 开发者板识别为虚拟 USB 网卡），再执行如下命令配置指定网卡的 IP 地址。

            **bash configure_usb_ethernet.sh -s** _usb_nic_name_ _ip_address_

            usb_nic_name：USB 网卡名称。

            ip_address：配置的 IP 地址。

            命令示例，配置 Ubuntu 服务器的 USB 网卡 IP 为 192.168.1.223：

            **bash configure_usb_ethernet.sh -s enp0s20f0u8 192.168.1.223**

        配置完成后，可执行**ifconfig**命令查看 IP 是否生效。
-   手工配置
    1.  以普通用户登录 Ubuntu 服务器，执行如下命令切换到 root 用户。
    2.  获取 USB 网卡名

        若系统中有多个 USB 网卡，可以通过拔插开发者板进行判定。
    3.  在 “/etc/netplan/01-netcfg.yaml” 文件中添加 USB 网卡的静态 IP。

        执行如下命令打开网络配置文件：

            vi /etc/netplan/01-netcfg.yaml

        在 ethernets 层下增加 USB 网卡网络配置，例如 USB 网卡名为_enp0s20f0u4_，配置静态 IP 为_192.168.1.223_，配置方法如下：

              ethernets: 
                ...
             _enp0s20f0u4_:
                  dhcp4: no
                  addresses: _\[192.168.1.223/24\]_
                  gateway4: _192.168.0.1_
                  nameservers:
                        addresses: _\[255.255.0.0\]_

        输入**:wq**保存退出。
    4.  执行如下命令重启网络服务。

        **netplan apply**

        重启后执行 ifconfig 命令，可查看 USB 网卡_enp0s20f0u4_的 IP 地址是否生效。

#### 使用网线直连 Ubuntu 服务器

Atlas 200 DK 开发者板与 Ubuntu 服务器通过网线直连的场景下，Atlas 200 DK NIC 网卡的默认地址为 192.168.0.2，子网掩码 24 位，所以需要修改 Ubuntu 服务器的 IP 地址为 192.168.0.x（x 取值范围为 0~1，3~254），使 Atlas 200 DK 与 Ubuntu 服务器可以通信。

![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
须知：

-   若制作 SD 卡时，您已修改 Atlas 200 DK 开发者板 NIC 网卡的 IP 地址与 Ubuntu 服务器的 NIC 网卡的 IP 地址在同一网段，则以下操作可直接跳过。

-   Atlas 200 DK 网口接入网线后，黄色的 ACT 灯闪烁，代表有数据传输。当是千兆以太网时，绿色的 LINK 灯会亮，当是百兆 / 10 兆的以太网时，LINK 灯正常不会亮。

配置方法如下：

1.  以普通用户登录 Ubuntu 服务器，执行如下命令切换到 root 用户。

2.  配置一个虚拟网卡 IP，用于与 Atlas 200 DK 进行通信。

    例如，配置_eth0:1_的虚拟静态 IP 地址。

        ifconfig eth0:1 192.168.0.223 netmask 255.255.0.0 up

#### （推荐）使用网线通过路由器连接 Ubuntu 服务器

Atlas 200 DK 通过网线接入路由器的场景下，需要开启路由器的 DHCP 功能，然后使用 DHCP 的方式自动分配 Atlas 200 DK 的 IP 地址，所以需要修改 Atlas 200 DK 的 NIC 网卡的 IP 地址获取方式为 DHCP。此时，需要同时使用 USB 方式连接 Ubuntu 服务器，然后通过 Ubuntu 服务器 SSH 登录到 Atlas 200 DK 进行 NIC 网卡的 IP 获取方式的修改。

假如您已经将 Atlas 200 DK 通过网线接入路由器，并开启了路由器的 DHCP 功能，后续操作步骤如下：

1.  使用 USB 连接线将 Atlas 200 DK 与 Ubuntu 服务器连接，并参考[通过 USB 端口直连 Ubuntu 服务器](#ZH-CN_TOPIC_0000001072723391__section16361183832019)配置 Ubuntu 服务器的 USB 网卡的 IP 地址，使其可与 Atlas 200 DK 通信。
2.  配置 Atlas 200 DK 的 NIC 网卡 IP 地址获取方式为 DHCP。
    1.  在 Ubuntu 服务器中，以 HwHiAiUser 用户 ssh 登录到 Atlas 200 DK 开发者板。Atlas 200 DK 的 USB 网卡的 IP 为制卡时设置的 USB IP。以默认 IP 地址 192.168.1.2 为例，执行如下命令登录：

        **ssh HwHiAiUser@192.168.1.2**

        用户 HwHiAiUser 缺省登录密码为**“Mind@123”**。
    2.  修改登录密码。首次登录时，系统将提示密码已过期：

            WARNING:Your password has expired.

        此时，必须修改密码并重新登录。请参见[HwHiAiUser 用户密码修改](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0015.html#ZH-CN_TOPIC_0000001073681284__section11164105125712)进行密码修改，修改完成后，系统会强制退出，并出现如下提示：

            passwd: pasword updated successfully
            Connection to 192.168.1.2 closed.

        请使用修改后的密码，执行如下命令重新登录：

        **ssh HwHiAiUser@192.168.1.2**
    3.  登录后，执行如下命令，切换到 root 用户：

        **su - root**

        此时系统会强制用户更改密码，请参见[root 用户密码修改](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0015.html#ZH-CN_TOPIC_0000001073681284__section611812112166)进行密码修改。
    4.  执行如下命令，打开网络配置文件。

        **vi /etc/netplan/01-netcfg.yaml**
    5.  修改 eth0 网卡的 IP 地址获取方式为 DHCP。

        把 eth0 的配置修改为如下内容：

                eth0:
                  dhcp4: true
                  addresses: \[\]
                  optional: true
    6.  保存退出。

        **:wq**
3.  执行如下命令重启网络服务。

    **netplan apply**

    至此，您可以在 Atlas 200 DK 开发者板上访问网络了。
4.  执行 ifconfig 命令获取 eth0 网卡的 IP 地址，您可以使用此 IP 地址与 Ubuntu 服务器通信，当然，您也可以继续使用 USB 网卡的 IP 地址与 Ubuntu 服务器通信。

#### 后续处理

Atlas 200 DK 开发者板连接 Ubuntu 服务器后，您可以根据 Atlas 200 DK 开发者板 LED 指示灯的状态决定是否重启 Atlas 200 DK 开发者板上的操作系统或对 Atlas 200 DK 开发者板执行断电操作，指示灯状态说明请参考[表 1](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0030.html#ZH-CN_TOPIC_0000001072935387__table686387333)

![](https://res-img2.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-notice.svg)
须知：

切忌不要随意重启或断电，尤其是 Atlas 200 DK 开发者板正在进行升级。 
 [https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0014.html](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0014.html)