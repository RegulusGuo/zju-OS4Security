# <center>riscv-gnu-toolchain安装与Linux内核编译教程（待完善）</center>


### 一、准备工作
0. **在进行之后的安装前，先确保虚拟机内存、磁盘足够。个人配置：内存3G，磁盘40G。具体扩容方法自行搜索；**
   ***注意：以下操作均在普通用户模式下进行，请不要切换到root用户，或随意使用sudo；***

1. 下载riscv-gnu-toolchain源码，链接：https://pan.zju.edu.cn/share/7793a0574d4b4dbeb85465f4ad

2. 将下载好的源码压缩包放入虚拟机合适的文件夹中，此处使用：~/Documents/linux4riscv/；

3. 在当前文件夹中解压（右键，“Extract Here”），得到文件夹riscv-gnu-toolchain；

4. 调整riscv-gnu-toolchain的权限：

   ```
   sudo chmod -R a+x riscv-gnu-toolchain
   ```

5. 安装所需预装软件包：

   ```
   sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev
   ```

6. 创建目录/home/cliff/opt/riscv并更改其权限

   ```
   cd /home
   sudo mkdir -p cliff/opt/riscv
   sudo chmod a+rw cliff/opt/riscv
   ```

7. 由于在此使用/home/cliff/opt/riscv作为安装文件夹，因此需要将/home/cliff/opt/riscv/bin加入环境变量PATH，具体方法参见链接：https://blog.csdn.net/White_Idiot/article/details/78253004
   
   特别注意：编辑前用chmod更改/etc/profile的权限，使其可写入；vim打开后，在文件最末尾添加：
   
   ```
   export PATH=$PATH:/home/cliff/opt/riscv/bin
   ```
   注意：更改之后记得按链接中所说，重启或用source指令更新，并用`echo $PATH`检查是否已加入PATH；

8. 将/home/cliff/opt/riscv设为可读写（同样使用chmod命令）；


### 二、编译安装
0. ***提示：`make`执行会比较花时间，请耐心等待***

1. 终端切到riscv-gnu-toolchain ***（进行操作前请务必确保当前处于正确的目录下，否则会出现`./configure: no such file or directory`等找不到文件的问题），*** 
   运行：

   ```
   bash ./configure --prefix=/home/cliff/opt/riscv
   ```

2. 若前一步正常，运行：`make`。这一步若出现`Permission denied`类型的报错，则重新调整一遍riscv-gnu-toolchain文件夹和/home/cliff/opt/riscv文件夹的权限，方法同前；

3. make结束后，运行：`riscv64-unknown-elf-gcc`，若提示：`fatal error: no input files`，说明riscv64-unknown-elf-gcc现在已经存在了；

4. 现在安装qemu，这一步中会有各种提示信息，内容是缺少某某软件包，按照提示的用apt安装即可。
   例：若提示：`ERROR: glib-2.40 gthread-2.0 is required to compile QEMU`，则参见链接https://blog.csdn.net/fuxy3/article/details/104732541
   以下是主要步骤

   ```
   cd qemu
   bash ./configure --target-list=riscv64-softmmu && make
   sudo make install
   ```

5. 上一步完成后，运行：`qemu-system-riscv64 -version` ，有正常版本号等信息说明成功；

6. 切回riscv-gnu-toolchain目录下，开始获取Linux内核源码。以下是从清华镜像站获取的：

   ```
   git clone https://mirrors.tuna.tsinghua.edu.cn/git/linux.git
   ```

7. 上一步获得的源码位于riscv-gnu-toolchain目录下的linux文件夹里，进行以下步骤：

   ```
   bash ./configure --prefix=/home/cliff/opt/riscv
   make linux
   ```

8. 上一步完成后，运行：`riscv64-unknown-linux-gnu-gcc`，若提示：`fatal error: no input files`，说明riscv64-unknown-linux-gnu-gcc现在已经存在了；

9. 编译内核：

   ```
   cd linux
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- defconfig
   make ARCH=riscv CROSS_COMPILE=riscv64-unknown-linux-gnu- -j $(nproc)
   ```

***注：以下步骤需根据老师选择使用的环境（老师说busybox有问题。。。）进行安装***

10. 下载busybox：

    ```
    git clone https://git.busybox.net/busybox
    cd busybox
    CROSS_COMPILE=riscv64-unknown-linux-gnu- make defconfig
    CROSS_COMPILE=riscv64-unknown-linux-gnu- make -j $(nproc)
    ```
后续工作尚未完成……
    

