# <center>riscv-gnu-toolchain安装教程（待完善）</center>

[toc]

### 一、准备工作

1. 下载riscv-gnu-toolchain源码，链接：https://pan.zju.edu.cn/share/7793a0574d4b4dbeb85465f4ad；

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

6.  创建目录==/home/cliff/opt/riscv==并更改其权限

    ```
   cd /home
   sudo mkdir -p cliff/opt/riscv
   sudo chmod a+rw cliff/opt/riscv
    ```

7. 由于在此使用==/home/cliff/opt/riscv==作为安装文件夹，因此需要将==/home/cliff/opt/riscv/bin==加入环境变量PATH，具体方法参见链接：https://blog.csdn.net/White_Idiot/article/details/78253004；
   注意：更改之后记得按链接中所说，重启或用source指令更新，并用`echo $PATH`检查是否已加入PATH；

8. 将/home/cliff/opt/riscv设为可读写（同样使用chmod命令）；

9. 在进行之后的安装前，先确保虚拟机内存、磁盘足够。个人配置：内存3G，磁盘40G。具体扩容方法自行搜索。

### 二、编译安装

1. 终端切到riscv-gnu-toolchain，运行：

   ```
   bash ./configure --prefix=/home/cliff/opt/riscv
   ```

2. 若前一步正常，运行：`make`

3. make结束后，运行：`riscv64-unknown-elf-gcc`，若提示：`fatal error: no input files`，说明riscv64-unknown-elf-gcc现在已经存在了；

4. 现在安装qemu，这一步中会有各种提示信息，内容是缺少某某软件包，按照提示的用apt安装即可。以下是主要步骤

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

10. 下载busybox：

    ```
    git clone https://git.busybox.net/busybox
    ```

    

