

```bash
# https://www.iteye.com/blog/haoningabc-2338061
# https://www.iteye.com/blog/haoningabc-2338061
# https://emscripten.org/docs/getting_started/downloads.html
# https://emscripten.org/docs/tools_reference/emcc.html#emccdoc
# https://www.cyberciti.biz/tips/compiling-linux-kernel-26.html
# https://emscripten.org/docs/tools_reference/emcc.html#emccdoc
# https://www.kernel.org/
# https://www.novell.com/documentation/suse91/suselinux-adminguide/html/ch11s03.html#:~:text=To%20configure%20the%20kernel%2C%20change,but%20loaded%20as%20a%20module.
# https://alpinelinux.org/downloads/
# https://github.com/buildroot/buildroot/tree/master/board/qemu/x86
# https://buildroot.org/downloads/manual/manual.html
# https://github.com/killinux/jslinux-tap
# https://github.com/killinux/jslinux-kernel
```

Steps to compile TinyEMU with Emscripten
```bash
sudo apt-get install libcurl4-openssl-dev libsdl2-dev

emmake make --makefile=Makefile.js
emmake make --makefile=Makefile.js clean
emmake make --makefile=Makefile.js --always-make

emmake make --makefile=Makefile_x86.js clean
emmake make --makefile=Makefile_x86.js
```




```bash
git clone https://github.com/buildroot/buildroot.git
cd buildroot
make qemu_x86_defconfig

make menuconfig
#	Toolchain > Enable WCHAR support
#	System configuration > System hostname --> ctfib
#	System configuration > System banner --> Hello Hacker. Welcome to CTFLib's Linux Playground.
#   System configuration > Init system (systemV)  ---> systemV
#   System configuration > /bin/sh (busybox' default shell)
#	System configuration > Enable root login with password
#	System configuration > Root password --> toor
#	System configuration > Custom scripts to run inside the fakeroot environment --> board/ctflib/post-build.sh
#	Target packages > Interpreter languages and scripting > python3
#	Target packages > Text editors and viewers > nano
```

```bash
mkdir board/ctflib
nano board/ctflib/post-build.sh
```
Contents
```bash
#!/bin/sh

set -u
set -e

# Add a console on tty1
if [ -e ${TARGET_DIR}/etc/inittab ]; then
    grep -qE '^tty1::' ${TARGET_DIR}/etc/inittab || \
        sed -i '/GENERIC_SERIAL/a\
tty1::respawn:/sbin/getty -L  tty1 0 vt100 # QEMU graphical window' ${TARGET_DIR}/etc/inittab
fi

cp board/ctflib/S00ctflib ${TARGET_DIR}/etc/init.d/S00ctflib
chmod 755 ${TARGET_DIR}/etc/init.d/S00ctflib

rm ${TARGET_DIR}/etc/init.d/S40network

```

```bash
nano board/ctflib/S00ctflib
```
Contents
```bash
#!/bin/sh
#
# UNIPI CTFLib - Linux Playground init script
#

# Show info
YELLOW='\033[1;33m'
NC='\033[0m' # No Color
echo -e "
${YELLOW}  ____     ______    ____     __        ______      _____      
 /\  __\  /\__  _\  /\  __\  /\ \      /\__  _\    /\  _ \     
 \ \ \_/  \/_/\ \/  \ \ \/_  \ \ \     \/_/\ \/    \ \ \L\ \   
  \ \ \  __  \ \ \   \ \  _\  \ \ \  __   \ \ \     \ \  _ <   
   \ \ \L\ \  \ \ \   \ \ \/   \ \ \L\ \   \_\ \__   \ \ \L\ \ 
    \ \____/   \ \_\   \ \_\    \ \____/   /\_____\   \ \____/ 
     \/___/     \/_/    \/_/     \/___/    \/_____/    \/___/  
                                                               
          ${NC}UNIPI CTFLib - ${YELLOW}Linux Playground by GramThanos        ${NC}
"

# Create home directory
mkdir -p /home
/bin/chmod 755 /home

# Add user
/usr/sbin/adduser -h /home/h4cker/ -s /bin/sh h4cker -D
echo -e "ctflib\nctflib" | /usr/bin/passwd h4cker > /dev/null 2>&1

# Create flags
echo "CTFLIB{L3aRN1ng_l1NuX_b4SIc5}" > /home/h4cker/user_flag.txt
/bin/chown h4cker:h4cker /home/h4cker/user_flag.txt
echo "CTFLIB{r00t_1S_tH3_S3rVer_4dMiN}" > /root/root_flag.txt

# Imporve shell
cat <<EOT >> /root/.profile
	export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
EOT
cp /root/.profile /home/h4cker/.profile
chown h4cker:h4cker /home/h4cker/.profile

```

```txt
# Enable drivers/net/e1000/ driver
make linux-menuconfig
Device drivers —>
    Network device support —>
        Ethernet driver support—>
        <*>     Intel(R) PRO/1000 Gigabit Ethernet support               
        <*>     Intel(R) PRO/1000 PCI-Express Gigabit Ethernet support
```


```bash
PATH=/home/thanos/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin

#make clean # (optional)
make -j12
```


```
cp output/images/rootfs.ext2 /mnt/c/Users/gramt/Downloads/jsLinux/debian/rootfs.ext2

cp output/images/rootfs.ext2 /mnt/c/Users/gramt/Downloads/CTFLib-jsLinux/distro/rootfs.ext2
cp output/images/kernel /mnt/c/Users/gramt/Downloads/CTFLib-jsLinux/distro/kernel
```


Image manipulation in Linux
```
dd if=/dev/zero of=ext4.img bs=4k count=4096
mkfs.ext4 ext4.img
tune2fs -c0 -i0 ext4.img
mkdir ext4
sudo mount ext4.img ext4/

wget https://dl-cdn.alpinelinux.org/alpine/latest-stable/releases/x86/alpine-minirootfs-3.16.0_rc5-x86.tar.gz
sudo tar xvzf alpine-minirootfs* -C ./ext4
sudo umount ext4
sudo rm -rf ext4




dd if=/dev/zero of=ext2.img bs=4k count=4096
mkfs.ext2 ext2.img
tune2fs -c0 -i0 ext2.img
mkdir ext2
sudo mount ext2.img ext2/

wget https://dl-cdn.alpinelinux.org/alpine/latest-stable/releases/x86/alpine-minirootfs-3.16.0_rc5-x86.tar.gz
sudo tar xvzf alpine-minirootfs* -C ./ext2
sudo umount ext2
sudo rm -rf ext2

dd if=/dev/zero of=ext2.img bs=1M count=64
```
