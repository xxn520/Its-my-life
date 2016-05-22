由于把旧的电脑带回家了，宝宝再也不能够在床上玩电脑了，可恶的是还要配置mac的网络。万恶的移动没有提供mac版的随意行，因此需要自己设置vpn，可怕！宝宝慌了，还好凭借宝宝的聪明脑瓜，把网络配置好了，先拿出来分享。至于其中原理，宝宝还要研究研究，下次分享哈！

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=26590186&auto=1&height=66"></iframe>

#### 第一步 买一个端口转换器

> 注：附上我的购买链接[点我点我。](https://detail.tmall.com/item.htm?_u=l22095huca16&id=524667361946)不过这个上网网速还不如实验室2M小水管，建议大家上京东买正版，可恶的奎君周不早说😒！

#### 第二步 连接网线、端口转换器、usb端口

> 此时会自动产生如下图选中的Apple USB Ethernet Adapter。

![](http://i2.buimg.com/2ccebf51fda84a4f.png)

#### 第三步 选择此处的创建PPPoE服务

![](http://i2.buimg.com/fbd4a285336f5a32.png)

#### 第四步 设置PPPoE服务相关参数，也就是宽带连接的用户名密码

![](http://i2.buimg.com/0439e1aa3491c16a.png)

#### 第五步 创建vpn，选择IPSec上的L2TP

![](http://ww2.sinaimg.cn/large/74311666jw1f43bdxaykmj21140va44u.jpg)

### 第六步 设置vpn相关参数，服务器地址和账户名，密码，并把高级设置里的通过VPN发送所有流量钩上

![](http://i2.buimg.com/313dab572cd9de9c.png)

### 第七步 增加 /etc/ppp/options 配置文件，内容如下图

![](http://i4.buimg.com/09da1eb1c2bff5ff.png)

![](http://i4.buimg.com/cb065aab4219b762.png)

