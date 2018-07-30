# androidFFmpeg4.0.2
在linux编译出android可用的最新版本 FFmpeg4.0.2

本人是用的是vmmare的编译环境是ubuntu17.10 
> 首先下载好vmmare，然后下载ubuntu的iso镜像。创建好ubuntu虚拟机<br>
> 然后在虚拟机上安装vvmare-tools，用来和win7共享文件,具体步骤可以google或者百度一下<br><br><br>

>然后在ubuntu上执行一下命令
> apt-get update <br>
> apt-get install yasm <br>
> apt-get install pkg-config<br><br><br>

> 下载ffmpeg和ndk（可以在虚拟机的目录中首先下载好，然后使用cp命令转移到linux系统中）<br>
> cp /mnt/hgfs/shareVM  /home/csh/shareVM  (/mnt/hgfs/shareVM是ubuntu中和win共享的路径，/home/csh/shareVM是本人接下来要操作的路径)<br><br><br>

> 下载ffmpeg   然后解压ffmpeg，本人的ffmpeg路径是  /home/csh/shareVM/ffmpeg-4.0.2<br>
> tar -zxvf 下载后的ffmpeg名称<br><br><br>

>  下载ndk 解压 本人的ndk路径是    /home/csh/shareVM/android-ndk-r17b-linux-x86_64.zip<br>
>  首先修改该目录的权限，暴力777权限全部搞定<br>
>  chamod 777 -R  /home/csh/shareVM/android-ndk-r17b-linux-x86_64.zip<br>
>  然后压缩<br>
>  unzip /home/csh/shareVM/android-ndk-r17b-linux-x86_64.zip<br>
>  配置环境变量 vim ~/.bashrc<br>
>  切换到输入模式  点击 按键 i 即可切换到输入模式 <br>
>  在最后面添加两行配置环境<br>
>  export NDKROOT=/home/csh/shareVM/android-ndk-r17b<br>
>  export PATH=$NDKROOT:$PATH<br>
>  然后点击 Esc 切换到命令行模式 输入 :wq  保存并且退出<br>
>  保存退出，更新一下环境变量   <br>
>  source ~/.bashrc  <br><br><br>

## 主题来了。要开始编译FFmpeg  <br>
>  cd /home/csh/shareVM/android-ndk-r17b  <br>
>  使用vim打开 configure  <br>
>  vi configure   <br>
>  找到如下代码  大概这个配置的49%的位置  <br>
>>  SLIBNAME_WITH_MAJOR='$(SLIBNAME).$(LIBMAJOR)'    <br>  
>>  LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'    <br>
>>  SLIB_INSTALL_NAME='$(SLIBNAME_WITH_VERSION)'    <br>
>>  SLIB_INSTALL_LINKS='$(SLIBNAME_WITH_MAJOR)$(SLIBNAME)'    <br>
> 修改成  <br>
>>  SLIBNAME_WITH_MAJOR='$(SLIBPREF)$(FULLNAME)-$(LIBMAJOR)$(SLIBSUF)'    <br>
>>  LIB_INSTALL_EXTRA_CMD='$$(RANLIB)"$(LIBDIR)/$(LIBNAME)"'    <br>
>>  SLIB_INSTALL_NAME='$(SLIBNAME_WITH_MAJOR)'    <br>
>>  SLIB_INSTALL_LINKS='$(SLIBNAME)'    <br>  <br>  <br>

>  在configure同级目录下新建build.sh  内容如下  <br>  <br>
> 新版的ffmpeg4.0以上删除了ffserver，不知道为什么要删除这个。如果知道请告知，感激不尽

>>  #!/bin/bash  <br>
>>  NDK=/home/csh/shareVM//android-ndk-r17b  <br>
>>  SYSROOT=$NDK/platforms/android-21/arch-arm/  <br>
>>  CPU=armv7-a  <br>
>>  TOOLCHAIN=$NDK/toolchains/arm-linux-androideabi-4.9/prebuilt/linux-x86_64  <br>
>>  PREFIX=$(pwd)/android/$CPU  <br>
>>  ADDI_CFLAGS="-marm"  <br>
>>  OPTIMIZE_CFLAGS="-mfloat-abi=softfp -mfpu=vfp -marm -march=$CPU "  <br>  <br>  <br>


>>  function build_android  <br>
>>  {  <br>
>>  #生成config.h  <br>
>>  ./configure \  <br>
>>  --prefix=$PREFIX \  <br>
>>  --enable-neon \  <br>
>>  --enable-hwaccels \  <br>
>>  --enable-shared \  <br>
>>  --enable-decoder=h264_mediacodec \  <br>
>>  --disable-static \  <br>
>>  --disable-doc \  <br>
>>  --disable-asm  \    <br>
>>  --enable-ffmpeg \  <br>
>>  --disable-ffplay \  <br>
>>  --disable-ffprobe \  <br>
>>  --enable-avdevice \  <br>
>>  --disable-doc \  <br>
>>  --disable-symver \  <br>
>>  --cross-prefix=$TOOLCHAIN/bin/arm-linux-androideabi- \  <br>
>>  --target-os=android \  <br>
>>  --arch=arm \  <br>
>>  --cpu=armv7-a \  <br>
>>  --enable-cross-compile \  <br>
>>  --sysroot=$SYSROOT \  <br>
>>  --extra-cflags="-Os -fpic $ADDI_CFLAGS" \  <br>
>>  --extra-ldflags="$ADDI_LDFLAGS" \  <br>
>>  $ADDITIONAL_CONFIGURE_FLAG  <br>  <br>  <br>


>>  make clean  <br>
>>  make -j4  <br>
>>  make install  <br>
>>  }  <br>
>>  build_android  <br>


>  赋予脚本执行权限：chmod +x build.sh 
>  执行脚本：./build.sh 

>  等待10分钟左右，在build.sh 同级目录下会生成android文件夹
>  如果此时想使用android里面的so文件，使用cp复制到虚拟机目录会有问题
>  cp -r /home/csh/shareVM/android/ /mnt/hgfs/shareVM/
>  由于文件系统不同，windows不支持链接，在复制cp的过程中会出现类似提示：
>    cp: cannot create symbolic link : Operation not permitted
>  所以可以使用一下命令行
>  zip -r -q -o /home/csh/shareVM/android.zip /mnt/hgfs/shareVM/

#  大功告成





