# DLAP221_修改Atlas 200 DK用户密码
更新时间：2021/02/08 GMT+08:00

#### 密码修改策略

环境搭建完成后，请修改 Atlas 200 DK 操作系统中用户的密码，以提升系统安全性。

操作系统用户的密码复杂度建议满足如下要求：

-   至少包含如下两种字符的组合：
    -   至少一个小写字母。
    -   至少一个大写字母。
    -   至少一个数字。
    -   至少一个特殊字符：\`~!@#$%^&\*()-\_=+\\\|\[{}];:'",&lt;.>/? 和空格。
-   长度至少 8 个字符。
-   不能与帐号或者帐号的倒写相同。

#### HwHiAiUser 用户密码修改

HwHiAiUser 用户为制作 SD 卡时创建的默认用户，此用户的默认密码是 “**Mind@123**”。Atlas 200 DK 开发者板与 Ubuntu 服务器连接成功后需要修改 Atlas 200 DK 开发者板中 HwHiAiUser 用户的初始密码。

1.  在 Ubuntu 服务器中以 HwHiAiUser 用户 ssh 登录到 Atlas 200 DK 开发者板。
2.  执行**passwd**命令修改 HwHiAiUser 密码。

    如[图 1](#ZH-CN_TOPIC_0000001073681284__fig111651159134813)所示。

    **图 1** 修改 HwHiAiUser 用户密码  
    ![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001072323515.png)

#### root 用户密码修改

1.  在 Ubuntu 服务器中以 HwHiAiUser 用户 ssh 登录 Atlas 200 DK 开发者板。

    ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
    说明：

    用户 HwHiAiUser 缺省登录密码为**Mind@123**。
2.  执行如下命令切换到 root 用户。

    **su - root**

    ![](https://res-img3.huaweicloud.com/content/dam/cloudbu-site/archive/china/zh-cn/support/resource/framework/v3/images/support-doc-new-note.svg)
    说明：

    root 用户的缺省登录密码为 Mind@123。
3.  执行**passwd**命令修改 root 用户的密码，如[图 2](#ZH-CN_TOPIC_0000001073681284__fig188111398242)所示。

    **图 2** 修改 root 用户密码  
    ![](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/figure/zh-cn_image_0000001072833177.png)

### 意见反馈

### 文档是否有解决您的问题？

提交成功！非常感谢您的反馈，我们会继续努力做到更好！ 
 [https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0015.html](https://support.huaweicloud.com/environment-deployment-Atlas200DK202/atlased_04_0015.html)