0.安装依赖
yum -y install gcc bc gcc-c++ ncurses ncurses-devel cmake elfutils-libelf-devel openssl-devel

1，下载内核
wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.14.186.tar.xz && tar -xvf linux-4.14.186.tar.xz && cd linux-4.14.186

2，替换文件
tar zxvf toa.tgz


3.清理内核源目录
make mrproper

4.使用make menuconfig菜单来订制内核功能 （这里我们使用默认直接保存即可）
make menuconfig

5.编译(时间很长,可以吃几局鸡再来)
make -j8

6.安装模块(进入解压目录安装模块,安装完毕后可以到/lib/modules目录下查看)
make modules_install
ll /lib/modules

7.执行make install 安装内核相关文件
make install

8.安装完成后修改默认内核启动项

cat /boot/grub2/grub.cfg | grep menuentry //查看系统可用内核 

grub2-set-default 'CentOS Linux (4.14.186) 7 (Core)' //修改开机默认使用的内核

9.重启电脑后uname -r查看内核版本

--------------------------------------------------------------------------

下载4.14内核源码
wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.14.91.tar.xz

解压
tar -Jxvf linux-4.14.91.tar.xz -C /root/

修改linux-4.14.91/include/net/inet_connection_sock.h，139行
u64 icsk_ca_priv[112 / sizeof(u64)];
#define ICSK_CA_PRIV_SIZE (14 * sizeof(u64))
这两段数值改为112和14，如上

修改/net/ipv4/tcp_output.c#L，1823行
tcp_snd_wnd_test函数大括号后}
换行添加EXPORT_SYMBOL(tcp_snd_wnd_test);

添加tcp_bbrplus.c，删除/net/ipv4/tcp_bbr.c
修改linux-4.14.91/net/ipv4/Makefile，
obj-$(CONFIG_TCP_CONG_BBR) += tcp_bbrplus.o，bbr改为bbrplus

安装依赖 centos
yum -y groupinstall Development tools
yum -y install ncurses-devel bc gcc gcc-c++ ncurses ncurses-devel cmake elfutils-libelf-devel openssl-devel rpm-build redhat-rpm-config asciidoc hmaccalc perl-ExtUtils-Embed xmlto audit-libs-devel binutils-devel elfutils-devel elfutils-libelf-devel newt-devel python-devel zlib-devel

debian
wget -qO- git.io/superupdate.sh | bash
apt-get install build-essential libncurses5-dev
apt-get build-dep linux

切换到目录 cd /root/linux-4.14.91

配置
make oldconfig
或者
make menuconfig

确保CONFIG_TCP_CONG_BBR=m

禁用签名调试
scripts/config --disable MODULE_SIG
scripts/config --disable DEBUG_INFO

开始编译
centos：make rpm-pkg

debian：make deb-pkg
