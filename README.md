# Hackintosh-Venue11pro

 dell venue 11 pro黑苹果安装  
  
在dell这块pc 平板二合一的小电脑逐渐跟不上时代的步伐，彻底从备胎机沦为吃灰机时，想着本来没有什么数据备份危险，就装个大家都说香的黑苹果，折腾折腾，装完后发现，居然这么香  
最终折腾结果为，历时一天半，时间成本还可以接受，驱动正常的为：cpu，核芯显卡，usb，蓝牙，键盘（除触摸板），屏幕触摸与A03电磁笔（有条件限制），声卡，网卡（直接用的usb外接网卡），足以满足一般使用要求
其他小毛病：
1.网卡可以换位内置无线网卡，但是目前博通这几款网卡卖的都太贵了，两百多的都不多，三百多的更是比比皆是，对于囊中羞涩的我直接选择放弃
2.还没有探索过电池管理
3.盖盖休眠后有一定几率出现虽然屏幕可以唤醒，但是处于卡死状态
4.系统字体偏小，无法通过更改屏幕分辨率的方法调节（会出现花屏等情况）

外界准备条件：多个u盘，usb hub（此平板只有一个usb接口，不够用），用于连接u盘和鼠标接收器

## mac系统安装过程

启动平板，并在出现dell图标的时候按住音量上键进入bios页面，选择bios设置，进行下列操作（我也不知道为什么要这么改，但是不止一个帖子指出usb3.0，intel vt等会导致安装失败，所以还是老老实实按他们的来吧，参考链接<https://www.insanelymac.com/forum/topic/302665-guide-1010-yosemite-on-the-dell-venue-11-pro-core-ix/>）
1.Under System Configuration > USB Configuration, disable "Enable USB 3.0 Controller"
2.Under Performance, disable the following (some of this will probably work but I haven't gotten around to seeing which ones do, it's best to just leave them disabled for now) :
Intel SpeedStep
C-states control
Intel Turboboost
Hyperthread control

制作mac install盘需要用transmac软件，先format，再restore disk image进去，这里我用的是一个带clover的macOS Sierra 10.12，链接地址为<https://www.cnblogs.com/semiconductorking/p/6536888.html>，这个镜像基本适配，就我的使用而言，除了声卡驱动外几乎完美。完成之后需要用disk genius把efi替换为我给的efi文件，这样mac制作盘才算完成

另取一个u盘制作为pe启动盘，这里推荐微pe工具箱，现在的微pe工具箱支持uefi启动，Under General > Boot sequence, 一定要把u盘启动设置在第一位，并且在以后每次需要使用u盘启动时都要这样设置一下，否则会使ssd作为启动项，之后应该就可以顺利进入pe，使用disk genius把所有的分区盘都删掉，然后新键盘，给esp分区到200m以上（有一些教程上说200m也可能不够，此处我设为了400m），新建efi文件夹，并把给的efi文件拷贝进去（也可以最后拷，因为装系统的时候efi用的是mac制作盘的），因为我不打算给这个平板做双系统，所以没有保留win的efi（v11有一个特性是有win的efi先使用该efi而不去寻找其他efi了），希望保留双系统的可以参考上面那个博客链接。这样设置完成后插入mac制作盘u盘，启动，在bios设置里将其设为第一顺位启动，应该就可以开始mac的漫长安装了

我在第一次安装的时候出现安装到最后阶段的时候，突然系统崩溃的情况，可能就是esp分配空间不够的原因，所以第二次安装的时候分配的空间大了一些。

## mac驱动

上述步骤完成后，cpu，gpu，蓝牙，键盘，usb等应该都是可以正常使用的，声卡（按博客链接的步骤我并没有做成功，又选择了其他方法）和触摸屏驱动如下
下载安装clover configuration，挂载进入ssd，把相关kext挂在./clover/kexts/other下即可，但是触摸驱动安装后会出现一段代码，并且需要多次启动才能进入系统的问题，所以我最后并没有使用这个驱动
VoodooHDA万能声卡，在contents的nfo.plist寻找一行文本：<key>IOPCIclassMatch</key><string>一串看不懂的</string>修改成：              <key>IOPCIPrimaryMatch</key><string>0x9c208086</string>（<数值>0x声卡dev id、ven id<地址>（我的电脑声卡dev id是9c20，ven id是8086，这个在windows下或者pe下进入设备管理器，找到High Definition Audio Controller选设备id即可得到），修改完成后，扔./clover/kexts/other里，并将其放入kext utility，输入密码，等待enjoy完成，再重启就发现声卡可以使用了
参考链接<https://jingyan.baidu.com/album/4b52d702469307bc5d774b4a.html?picindex=6>
