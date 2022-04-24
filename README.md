```
root@localhost:~# apt-get install git ccache automake flex lzop bison gperf build-essential zip curl zlib1g-dev  g++-multilib  libxml2-utils bzip2 libbz2-dev libbz2-1.0 libghc-bzlib-dev squashfs-tools pngcrush schedtool dpkg-dev liblz4-tool make optipng maven libssl-dev pwgen libswitch-perl policycoreutils minicom libxml-sax-base-perl libxml-simple-perl bc  lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev libgl1-mesa-dev xsltproc unzip  python3 git  gcc make flex bison util-linux kmod  e2fsprogs jfsutils reiserfsprogs xfsprogs squashfs-tools btrfs-progs pcmciautils "*quota-*" ppp "*isdn*" nfs*-*utils  procps oprofile udev grub2-common "*mcelog*" iptables openssl  bc  libssl-dev gcc-multilib   build-essential -y
```
## Create Same tree as previous build time 
```
mkdir -p workspace/source/prebuilts/gcc/linux-x86/aarch64 workspace/source/prebuilts/build-tools/linux-x86 workspace/source/prebuilts/clang/host/linux-x86/ workspace/source/kernel/msm-4.19 workspace/source/kernel/kernel_out mkdir -p workspace/source/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9/
```
## Install toolchains and make tools for kernel and extract them to their proper place
```
wget https://android.googlesource.com/platform/prebuilts/clang/host/linux-x86/+archive/refs/heads/android-gs-raviole-5.10-android12-d1/clang-r383902.tar.gz // TO ~workspace/source/prebuilts/clang/host/linux-x86/

wget https://android.googlesource.com/platform/prebuilts/build-tools/+archive/refs/heads/master/linux-x86.tar.gz // TO ~workspace/source/prebuilts/build-tools/

wget https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9/+archive/refs/heads/android-msm-barbet-4.19-android12-qpr1.tar.gz // TO ~workspace/source/prebuilts/gcc/linux-x86/aarch64
```
## BUILD SCRIPT
```
#2. export some environment variable and build the kernel and dtb

export SOURCE_ROOT=~/workspace/source
export DEFCONFIG=vendor/kona-perf_defconfig
export MAKE_PATH=${SOURCE_ROOT}/prebuilts/build-tools/linux-x86/bin/
export CROSS_COMPILE=${SOURCE_ROOT}/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9/bin/aarch64-linux-android-
export KERNEL_ARCH=arm64
export KERNEL_DIR=${SOURCE_ROOT}/kernel/msm-4.19
export KERNEL_OUT=${KERNEL_DIR}/../kernel_out
export KERNEL_SRC=${KERNEL_OUT}
export CLANG_TRIPLE=aarch64-linux-gnu-
export OUT_DIR=${KERNEL_OUT}
export ARCH=${KERNEL_ARCH}
export TARGET_INCLUDES=${TARGET_KERNEL_MAKE_CFLAGS}
export TARGET_LINCLUDES=${TARGET_KERNEL_MAKE_LDFLAGS}

export TARGET_KERNEL_MAKE_ENV+="CC=${SOURCE_ROOT}/prebuilts/clang/host/linux-x86/clang-r383902/bin/clang"

cd ${KERNEL_DIR} && \
${MAKE_PATH}make O=${OUT_DIR} ${TARGET_KERNEL_MAKE_ENV} HOSTLDFLAGS="${TARGET_LINCLUDES}" ARCH=${ARCH} CROSS_COMPILE=${CROSS_COMPILE}

cd ${OUT_DIR} && \
${MAKE_PATH}make ARCH=${ARCH} CROSS_COMPILE=${CROSS_COMPILE} HOSTCFLAGS="${TARGET_INCLUDES}" HOSTLDFLAGS="${TARGET_LINCLUDES}" O=${OUT_DIR} ${TARGET_KERNEL_MAKE_ENV}

```
