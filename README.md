# Linux From Scratch - v12.0

## prepare the VM 
## II. Preparing for the Build 
### Chapter 2. Preparing the Host System 

#### 2.2 Host System Requirements 
- install the required packages
```shell
sudo apt install bison gawk m4 texinfo 
sudo ln -sf bash /usr/bin/sh # set the sh shell to bash
```
- run the version check script
```shell
d3bug@vbox:~/LinuxFromScratch$ ./version-check.sh 

OK:    Coreutils 8.32   >= 7.0
OK:    Bash      5.1.16 >= 3.2
OK:    Binutils  2.38   >= 2.13.1
OK:    Bison     3.8.2  >= 2.7
OK:    Diffutils 3.8    >= 2.8.1
OK:    Findutils 4.8.0  >= 4.2.31
OK:    Gawk      5.1.0  >= 4.0.1
OK:    GCC       11.3.0 >= 5.1
OK:    GCC (C++) 11.3.0 >= 5.1
OK:    Grep      3.7    >= 2.5.1a
OK:    Gzip      1.10   >= 1.3.12
OK:    M4        1.4.18 >= 1.4.10
OK:    Make      4.3    >= 4.0
OK:    Patch     2.7.6  >= 2.5.4
OK:    Perl      5.34.0 >= 5.8.8
OK:    Python    3.10.6 >= 3.4
OK:    Sed       4.8    >= 4.1.5
OK:    Tar       1.34   >= 1.22
OK:    Texinfo   6.8    >= 5.0
OK:    Xz        5.2.5  >= 5.0.0
OK:    Linux Kernel 5.19.0 >= 4.14
OK:    Linux Kernel supports UNIX 98 PTY
Aliases:
OK:    awk  is GNU
OK:    yacc is Bison
OK:    sh   is Bash
Compiler check:
OK:    g++ works

```

#### 2.4. Creating a New Partition 
- list the partition 
```shell
d3bug@vbox:~/LinuxFromScratch$ sudo fdisk -l /dev/sdb

Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes

```
- create a GPT partition 
```shell
d3bug@vbox:~/LinuxFromScratch$ sudo fdisk /dev/sdb

Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0x58f5ed83.

Command (m for help): g
Created a new GPT disklabel (GUID: 59295BBC-3F1A-5D42-8CE4-1123B5E3D435).

Command (m for help): n
Partition number (1-128, default 1): 
First sector (2048-62914526, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-62914526, default 62914526): 

Created a new partition 1 of type 'Linux filesystem' and of size 30 GiB.

Command (m for help): p

Disk /dev/sdb: 30 GiB, 32212254720 bytes, 62914560 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 59295BBC-3F1A-5D42-8CE4-1123B5E3D435

Device     Start      End  Sectors Size Type
/dev/sdb1   2048 62914526 62912479  30G Linux filesystem

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

#### 2.5. Creating a File System on the Partition 
- format the partition to `ext4`
```shell
d3bug@vbox:~/LinuxFromScratch$ sudo mkfs -v -t ext4 /dev/sdb1
mke2fs 1.46.5 (30-Dec-2021)
fs_types for mke2fs.conf resolution: 'ext4'
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
1966080 inodes, 7864059 blocks
393202 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=2155872256
240 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Filesystem UUID: 52099d7c-4b1a-46dc-8340-344583a09fc7
Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information:   0done   

```

#### 2.6. Setting The $LFS Variable 
```shell
d3bug@vbox:~/LinuxFromScratch$ export LFS=/mnt/lfs
d3bug@vbox:~/LinuxFromScratch$ sudo -E env | grep lfs
LFS=/mnt/lfs

```

#### 2.7. Mounting the New Partition 

```shell 
d3bug@vbox:~/LinuxFromScratch$ sudo -E mkdir -pv $LFS
mkdir: created directory '/mnt/lfs'

d3bug@vbox:~/LinuxFromScratch$ sudo -E mount -v -t ext4 /dev/sdb1 $LFS
mount: /dev/sdb1 mounted on /mnt/lfs.

root@vbox:/home/d3bug/LinuxFromScratch# mount | grep -i lfs
/dev/sdb1 on /mnt/lfs type ext4 (rw,relatime)

```


### Chapter 3. Packages and Patches 
- create a dir for packages
```shell
root@vbox:/home/d3bug/LinuxFromScratch# mkdir -v $LFS/sources
mkdir: created directory '/mnt/lfs/sources'

root@vbox:/mnt/lfs# chmod -v a+wt $LFS/sources
mode of '/mnt/lfs/sources' changed from 0755 (rwxr-xr-x) to 1777 (rwxrwxrwt)

```
- download the packages
```shell 
root@vbox:/mnt/lfs# wget https://www.linuxfromscratch.org/lfs/view/stable/wget-list-sysv

root@vbox:/mnt/lfs# wget --input-file=wget-list-sysv --continue --directory-prefix=$LFS/sources

```

download size - 500Mb


### Chapter 4. Final Preparations 

####  4.2. Creating a Limited Directory Layout in the LFS Filesystem 

```shell
root@vbox:/mnt/lfs# mkdir -pv $LFS/{etc,var} $LFS/usr/{bin,lib,sbin}

for i in bin lib sbin; do
  ln -sv usr/$i $LFS/$i
done

case $(uname -m) in
  x86_64) mkdir -pv $LFS/lib64 ;;
esac

mkdir: created directory '/mnt/lfs/etc'
mkdir: created directory '/mnt/lfs/var'
mkdir: created directory '/mnt/lfs/usr'
mkdir: created directory '/mnt/lfs/usr/bin'
mkdir: created directory '/mnt/lfs/usr/lib'
mkdir: created directory '/mnt/lfs/usr/sbin'
'/mnt/lfs/bin' -> 'usr/bin'
'/mnt/lfs/lib' -> 'usr/lib'
'/mnt/lfs/sbin' -> 'usr/sbin'
mkdir: created directory '/mnt/lfs/lib64

root@vbox:/mnt/lfs# mkdir -pv $LFS/tools
mkdir: created directory '/mnt/lfs/tools'
```
####  4.3. Adding the LFS User 
```shell
root@vbox:/mnt/lfs# groupadd lfs
useradd -s /bin/bash -g lfs -m -k /dev/null lfs

root@vbox:/mnt/lfs# chown -v lfs $LFS/{usr{,/*},lib,var,etc,bin,sbin,tools}
case $(uname -m) in
  x86_64) chown -v lfs $LFS/lib64 ;;
esac
changed ownership of '/mnt/lfs/usr' from root to lfs
changed ownership of '/mnt/lfs/usr/bin' from root to lfs
changed ownership of '/mnt/lfs/usr/lib' from root to lfs
changed ownership of '/mnt/lfs/usr/sbin' from root to lfs
ownership of '/mnt/lfs/lib' retained as lfs
changed ownership of '/mnt/lfs/var' from root to lfs
changed ownership of '/mnt/lfs/etc' from root to lfs
ownership of '/mnt/lfs/bin' retained as lfs
ownership of '/mnt/lfs/sbin' retained as lfs
changed ownership of '/mnt/lfs/tools' from root to lfs
changed ownership of '/mnt/lfs/lib64' from root to lfs

```
####  4.4. Setting Up the Environment 
```shell
lfs@vbox:~$ cat > ~/.bashrc << "EOF"
set +h
umask 022
LFS=/mnt/lfs
LC_ALL=POSIX
LFS_TGT=$(uname -m)-lfs-linux-gnu
PATH=/usr/bin
if [ ! -L /bin ]; then PATH=/bin:$PATH; fi
PATH=$LFS/tools/bin:$PATH
CONFIG_SITE=$LFS/usr/share/config.site
export LFS LC_ALL LFS_TGT PATH CONFIG_SITE
EOF

root@vbox:/mnt/lfs# [ ! -e /etc/bash.bashrc ] || mv -v /etc/bash.bashrc /etc/bash.bashrc.NOUSE
renamed '/etc/bash.bashrc' -> '/etc/bash.bashrc.NOUSE'


```

##  III. Building the LFS Cross Toolchain and Temporary Tools 

###  Chapter 5. Compiling a Cross-Toolchain 
####  5.2. Binutils-2.41 - Pass 1 
```shell

lfs:/mnt/lfs/sources$ tar xf binutils-2.41.tar.xz
lfs:/mnt/lfs/sources$ cd binutils-2.41
lfs:/mnt/lfs/sources/binutils-2.41$  mkdir -v build  && cd  build
mkdir: created directory 'build'

lfs:/mnt/lfs/sources/binutils-2.41/build$ ../configure --prefix=$LFS/tools \
                                        --with-sysroot=$LFS \
                                        --target=$LFS_TGT   \
                                        --disable-nls       \
                                        --enable-gprofng=no \
                                        --disable-werror
lfs:/mnt/lfs/sources/binutils-2.41/build$ make && make install
lfs:/mnt/lfs/sources$ rm -rf binutils-2.41

```

#### 5.3. GCC-13.2.0 - Pass 1 
```shell
lfs:/mnt/lfs/sources/gcc-13.2.0$ tar xf gcc-13.2.0.tar.xz && cd gcc-13.2.0

lfs:/mnt/lfs/sources/gcc-13.2.0$ tar -xf ../mpfr-4.2.0.tar.xz
mv -v mpfr-4.2.0 mpfr
tar -xf ../gmp-6.3.0.tar.xz
mv -v gmp-6.3.0 gmp
tar -xf ../mpc-1.3.1.tar.gz
mv -v mpc-1.3.1 mpc
renamed 'mpfr-4.2.0' -> 'mpfr'
renamed 'gmp-6.3.0' -> 'gmp/gmp-6.3.0'
renamed 'mpc-1.3.1' -> 'mpc'

lfs:/mnt/lfs/sources/gcc-13.2.0$ case $(uname -m) in
  x86_64)
    sed -e '/m64=/s/lib64/lib/' \
        -i.orig gcc/config/i386/t-linux64
 ;;
esac
lfs:/mnt/lfs/sources/gcc-13.2.0$ mkdir -v build && cd build

lfs:/mnt/lfs/sources/gcc-13.2.0/build$ ../configure                  \
                                    --target=$LFS_TGT         \
                                    --prefix=$LFS/tools       \
                                    --with-glibc-version=2.38 \
                                    --with-sysroot=$LFS       \
                                    --with-newlib             \
                                    --without-headers         \
                                    --enable-default-pie      \
                                    --enable-default-ssp      \
                                    --disable-nls             \
                                    --disable-shared          \
                                    --disable-multilib        \
                                    --disable-threads         \
                                    --disable-libatomic       \
                                    --disable-libgomp         \
                                    --disable-libquadmath     \
                                    --disable-libssp          \
                                    --disable-libvtv          \
                                    --disable-libstdcxx       \
                                    --enable-languages=c,c++

lfs:/mnt/lfs/sources/gcc-13.2.0/build$ make && make install
lfs:/mnt/lfs/sources/gcc-13.2.0/build$ cd .. \
&& cat gcc/limitx.h gcc/glimits.h gcc/limity.h > \
`dirname $($LFS_TGT-gcc -print-libgcc-file-name)`/include/limits.h

```

####  5.4. Linux-6.4.12 API Headers 
```shell
lfs:/mnt/lfs/sources$ tar xf linux-6.4.12.tar.xz && cd linux-6.4.12
lfs:/mnt/lfs/sources/linux-6.4.12$ make mrproper
lfs:/mnt/lfs/sources/linux-6.4.12$ make headers \
                                    && find usr/include -type f ! -name '*.h' -delete \
                                    && cp -rv usr/include $LFS/usr \


```

####  5.5. Glibc-2.38 
```shell
lfs:/mnt/lfs/sources$ tar xf glibc-2.38.tar.xz && cd glibc-2.38
lfs:/mnt/lfs/sources/glibc-2.38$ case $(uname -m) in
    i?86)   ln -sfv ld-linux.so.2 $LFS/lib/ld-lsb.so.3
    ;;
    x86_64) ln -sfv ../lib/ld-linux-x86-64.so.2 $LFS/lib64
            ln -sfv ../lib/ld-linux-x86-64.so.2 $LFS/lib64/ld-lsb-x86-64.so.3
    ;;
esac

'/mnt/lfs/lib64/ld-linux-x86-64.so.2' -> '../lib/ld-linux-x86-64.so.2'
'/mnt/lfs/lib64/ld-lsb-x86-64.so.3' -> '../lib/ld-linux-x86-64.so.2'

lfs:/mnt/lfs/sources/glibc-2.38$ patch -Np1 -i ../glibc-2.38-fhs-1.patch
patching file Makeconfig
Hunk #1 succeeded at 262 (offset 12 lines).
patching file nscd/nscd.h
Hunk #1 succeeded at 160 (offset 48 lines).
patching file nss/db-Makefile
Hunk #1 succeeded at 21 (offset -1 lines).
patching file sysdeps/generic/paths.h
patching file sysdeps/unix/sysv/linux/paths.h

lfs:/mnt/lfs/sources/glibc-2.38$ mkdir -v build && cd  build
mkdir: created directory 'build'

lfs:/mnt/lfs/sources/glibc-2.38/build$ echo "rootsbindir=/usr/sbin" > configparms

lfs:/mnt/lfs/sources/glibc-2.38/build$ ../configure                             \
      --prefix=/usr                      \
      --host=$LFS_TGT                    \
      --build=$(../scripts/config.guess) \
      --enable-kernel=4.14               \
      --with-headers=$LFS/usr/include    \
      libc_cv_slibdir=/usr/lib
lfs:/mnt/lfs/sources/glibc-2.38/build$ make
lfs:/mnt/lfs/sources/glibc-2.38/build$ make DESTDIR=$LFS install
lfs:/mnt/lfs/sources/glibc-2.38/build$ sed '/RTLDLIST=/s@/usr@@g' -i $LFS/usr/bin/ldd


```