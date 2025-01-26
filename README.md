玩客云刷机步骤
# onecloud
玩客云安装CasaOS(Openwrt、HiNAS)教程

一、刷机过程
晶晨系列 S805 CPU 机型，又称玩客云，赚3，ws1608, onecloud；

直接使用晶晨烧录工具刷入作者编译的固件。

一、准备工作
1、准备一个双公头 USB 连接线

2、拆开盒子，取出主板
拆解外壳技巧：
先热用吹风机把背后的pvc贴片吹软，然后用刀具小心撬开。

3、准备晶晨烧录软件
下载和安装好：晶晨烧录工具: USB_Burning_Tool.exe

二、采用线刷烧写刷入海纳思系统、OpenWRT、CasaOS

1. 找到主板短接点：
不同板子请自行百度短接位置，下面提供作者收集到的图例：

2. 双公头 usb 连接：
双公头 usb 连接线一头接电脑，一头接板子2号口（靠近 HDMI 的一边）
或者具有Type-C接口的电脑和手机充电线

3. 导入海纳思系统（OpenWRT、CasaOS）线刷包
打开 USB_Burning_Tool 软件工具，导入刚才解压后的以 img 结尾的固件包

4. 烧写海纳思系统（OpenWRT、CasaOS）线刷包
方法1：短接住-->打开盒子电源-->显示已连接-->放开短接-->点击 “开始” 按钮
方法2：点击 “开始” 按钮-→短接住-->打开盒子电源-->显示已连接-->放开短接

5. 拔掉双公头、关闭软件。
最后，拔掉双公头连接线，关闭软件。至此，系统烧写刷入完成。
6、插上网线，重新通电。
玩客云指示灯会变换好几个颜色，最后通过路由器后台查看，名称为 onecloud 的新设备接入网络，刷机成功！
方法二：用于已刷过系统的设备
1、下载直刷包并解压缩。
2、导入海纳思系统（OpenWRT、CasaOS）线刷包
打开 USB_Burning_Tool软件，导入刚才解压后的以 img 结尾的固件包
3、烧写海纳思系统（OpenWRT、CasaOS）线刷包
设备先不要通电，使用双公头USB线，一头连接玩客云靠近 HDMI 口的 USB 口，另一头插电脑。
用 卡针或大头针 长按 复位键（具体见图）的同时，并接通设备电源。听到 电脑发出类似插入U盘的声音，电脑烧录软件会出现烧录进度，此时复位键不要松开，再按个十几秒再松开。 
4. 拔掉双公头、关闭软件。
最后，拔掉双公头连接线，关闭软件。至此，系统烧写刷入完成。
5、插上网线，重新通电。
玩客云指示灯会变换好几个颜色，最后通过路由器后台查看，名称为 onecloud 的新设备接入网络，刷机成功！

三、安装软件安装包
1、安装海纳思系统
（1）重新上电，等待 2-3 分钟，首次初始化启动完毕
等待 2-3 分钟，首次启动完毕，即可到路由器查找自动分配的 IP；
这个 IP 在你的路由器客户端列表中，显示的 mac 地址是
00:11:22:33:44:66
​
（2） 浏览器打开该 IP 地址，进入 web 管理页面
后续具体步骤见 ：https://www.ecoo.top/docs/tutorial-extras/start/
2、安装CasaOS
 （1）使用SSH客户端登录玩客云，使用NxShell。初始用户名和密码为：

Username: root
Password: 1234
​

（2） 登录后提示修改密码。改完密码后，提示创建用户，直接按ctrl+c取消。选择bash，输入1。

（3）一键安装脚本：
bash <(wget -qO- https://play.cuse.eu.org/casaos_newbie.sh)
​
输入功能序号：3，选择安装CasaOS。

注：不要更改系统软件源，否则会提示以下信息

Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

如已更改系统软件源，建议从新刷机。
等待安装过程结束。
  
（4）安装结束后打开在路由器中名为onecloud的IP地址
 
（5）注册CasaOS用户
 
进入CasaOS用户界面。
 
更改docker
详细见参考2

四、安装 OpenWRT

具体代码
 
点击CasaOS界面左上角第三个图标并输入用户名和密码

1、先打开网卡混杂模式

ip link set eth0 promisc on
​
2、创建网络:你的网络是多少段X就填多少，其他的数字不要改!

docker network create -d macvlan --subnet=192.168.X.0/24 --gateway=192.168.X.1 -o parent=eth0 openwrt
​
举个例子，我的onecloud的ip是192.168.68.220，所以上面代码的两个X换成68即可

3、下载openwrt镜像文件
https://openwrt.ai/
 
4、上传openwrt镜像文件至CasaOS文件夹

打开CasaOS界面APP中的Files，选择Root文件夹下的root子文件夹，上传步骤3下载的压缩包。

5、修改生成镜像的命令为：

docker import kwrt-09.26.2024-amlogic-meson8b-thunder-onecloud-rootfs.tar.gz onecloud/openwrt
​

将上述kwrt-09.26.2024-amlogic-meson8b-thunder-onecloud-rootfs.tar.gz修改为你下载的压缩包名。
6、安装并启动容器：

docker run --restart always --name openwrt -d --network openwrt --privileged onecloud/openwrt /sbin/init
​
7、修改进入Openwrt的IP地址
#进入容器

docker exec -it openwrt bash
​
#打开网络配置文件

nano /etc/config/network
​
这一步可以看到IP为 10.0.0.1，移动光标修改成你的玩客云现在的ip地址，
然后ctrl+o，回车，ctrl+x退出即可
#重启网络

/etc/init.d/network restart
​
8、关闭终端界面

五、设置openwrt为旁路由
1、进入 openwrt
打开在步骤三-7中修改的IP地址。
 
账户是 root ，密码是 root
2、具体步骤设置

下一步我们就需要在路由器上进行一个简单的设置了。
进入路由器的后台，我们选择“内部网络—DHCP服务器”。
此时我们有两处需要更改：默认网关和DNS服务器！这两个地方全部改成玩客云的IP地址！
然后点击路由器的“保存或者应用”。此时我们的网络可能会有短暂的重启（大概10秒种），重启过后就正常了。

参考文章：
[1]：https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8356307&highlight=CasaOS
[2]：https://www.ecoo.top/docs/tutorial-basics/s805/
[3]：https://baijiahao.baidu.com/s?id=1805624062043725942&wfr=spider&for=pc
[4]：https://post.smzdm.com/p/aqqxe9k2/
[5]：https://anubis.cafe/b0255f1
