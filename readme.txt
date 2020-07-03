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

