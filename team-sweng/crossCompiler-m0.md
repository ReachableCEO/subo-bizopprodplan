
# Cross Compiler Notes - cotextm0

Begin active work (other than git clone and such ) at 18:09 on 05/28/2021

Quick look at bash history (to document git clones)

738  02/19/2021 20:56:24 git clone --depth=1 https://github.com/raspberrypi/linux
874  03/02/2021 21:04:48 git clone git://sourceware.org/git/newlib-cygwin.git
876  03/02/2021 21:03:33 git clone git://sourceware.org/git/binutils-gdb.git
897  03/02/2021 20:52:16 git clone  git://gcc.gnu.org/git/gcc.git
914  03/02/2021 21:02:45 git clone https://github.com/crosstool-ng/crosstool-ng.git
1099  03/07/2021 15:22:03 git clone https://github.com/librenms/librenms-agent.git
1497  05/28/2021 13:58:59 git clone git://gcc.gnu.org/git/gcc.git
1513  05/28/2021 18:08:05 history |grep git|grep clone

So this has been on again/off again for about two months. Glad to be wrapping it up.

Attempting to run the build script (./build-prerequisites.sh) gives us an error:

```console
+2021-05-28:18:13:57 (./build-prerequisites.sh:132): echo Task '[I-0]' /x86_64-linux-gnu/zlib/
Task [I-0] /x86_64-linux-gnu/zlib/
+2021-05-28:18:13:57 (./build-prerequisites.sh:133): rm -rf /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-native/zlib
+2021-05-28:18:13:57 (./build-prerequisites.sh:134): copy_dir_clean /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/src/zlib-1.2.8 /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-native/zlib
+2021-05-28:18:13:57 (/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh:50): copy_dir_clean(): set +u
+2021-05-28:18:13:57 (/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh:51): copy_dir_clean(): mkdir -p /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-native/zlib
+2021-05-28:18:13:57 (/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh:52): copy_dir_clean(): cd /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/src/zlib-1.2.8
/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh: line 52: cd: /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/src/zlib-1.2.8: No such file or directory
+2021-05-28:18:13:57 (/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh:56): copy_dir_clean(): cd /subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-native/zlib
+2021-05-28:18:13:57 (/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major/build-common.sh:56): copy_dir_clean(): tar xf -
tar: This does not look like a tar archive
tar: Exiting with failure status due to previous errors
root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# ls build-native/
zlib
root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# ls build-native/
.stage  zlib/
root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# ls build-native/zlib/
```

Which is expected as zlib isn't downloaded, though it arguably should be by install-sources.sh which utilizes build-common.sh which is where you find the 
actual download URLs. Light edit I did, was changing URLs from ftp to http.  Between release.txt and the How-to-build-toolchain.pdf I got the following results:

```console
root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# ls src/
binutils-gdb  gcc  gmp-6.1.0.tar.bz2  isl-0.18.tar.xz  mpc-1.0.3.tar.gz  mpfr-3.1.4.tar.bz2  newlib-cygwin
root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major#
```


Since this was written for ubuntu 14.04 , i'm guessing URLs are out of date.... so...

```
GMP_URL=https://gmplib.org/download/gmp/$GMP_PACK
MPFR_URL=http://www.mpfr.org/$MPFR/$MPFR_PACK
MPC_URL=http://ftp.gnu.org/gnu/mpc/$MPC_PACK
ISL_URL=http://isl.gforge.inria.fr/$ISL_PACK
EXPAT_URL=https://downloads.sourceforge.net/project/expat/expat/$EXPAT_VER/$EXPAT_PACK
LIBELF_URL=https://fossies.org/linux/misc/old/$LIBELF_PACK
LIBICONV_URL=https://ftp.gnu.org/pub/gnu/libiconv/$LIBICONV_PACK
ZLIB_URL=http://www.zlib.net/fossils/$ZLIB_PACK
ENV_VAR_UPDATE_URL=http://nsis.sourceforge.net/mediawiki/images/a/ad/$ENV_VAR_UPDATE_PACK
PYTHON_WIN_URL=https://www.python.org/ftp/python/$PYTHON_WIN_VER/$PYTHON_WIN_PACK
BUGURL="https://developer.arm.com/open-source/gnu-toolchain/gnu-rm"

root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# grep PACK build-common.sh
PACKAGEDIR=$ROOT/pkg
GMP_PACK=$GMP.tar.bz2
MPFR_PACK=$MPFR.tar.bz2
MPC_PACK=$MPC.tar.gz
ISL_PACK=$ISL.tar.xz
EXPAT_PACK=$EXPAT.tar.bz2
LIBELF_PACK=$LIBELF.tar.gz
LIBICONV_PACK=$LIBICONV.tar.gz
ZLIB_PACK=$ZLIB.tar.gz
ENV_VAR_UPDATE_PACK=$ENV_VAR_UPDATE.7z
PYTHON_WIN_PACK=$PYTHON_WIN.msi
GMP_URL=https://gmplib.org/download/gmp/$GMP_PACK
MPFR_URL=http://www.mpfr.org/$MPFR/$MPFR_PACK
MPC_URL=http://ftp.gnu.org/gnu/mpc/$MPC_PACK
ISL_URL=http://isl.gforge.inria.fr/$ISL_PACK
EXPAT_URL=https://downloads.sourceforge.net/project/expat/expat/$EXPAT_VER/$EXPAT_PACK
LIBELF_URL=https://fossies.org/linux/misc/old/$LIBELF_PACK
LIBICONV_URL=https://ftp.gnu.org/pub/gnu/libiconv/$LIBICONV_PACK
ZLIB_URL=http://www.zlib.net/fossils/$ZLIB_PACK
ENV_VAR_UPDATE_URL=http://nsis.sourceforge.net/mediawiki/images/a/ad/$ENV_VAR_UPDATE_PACK
PYTHON_WIN_URL=https://www.python.org/ftp/python/$PYTHON_WIN_VER/$PYTHON_WIN_PACK
PACKAGE_NAME_SUFFIX="${host_arch}-linux"
PACKAGE_NAME_SUFFIX=mac-$(sw_vers -productVersion)
PACKAGE_NAME=gcc-$TARGET-$GCC_VER_NAME-$RELEASEVER
PACKAGE_NAME_NATIVE=$PACKAGE_NAME-$PACKAGE_NAME_SUFFIX
PACKAGE_NAME_MINGW=$PACKAGE_NAME-win32
INSTALL_PACKAGE_NAME=gcc-$TARGET-$GCC_VER_NAME-$RELEASEVER

root@buildbox:/subo/Tooling/src/team-sweng/cross-compile/arduino-m0/gcc-arm-none-eabi-10-2020-q4-major# grep VER build-common.sh
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
WORKSPACE | SRC_VERSION)
GMP_VER=6.1.0
MPFR_VER=3.1.4
MPC_VER=1.0.3
ISL_VER=0.18
EXPAT_VER=2.1.1
LIBELF_VER=0.8.13
LIBICONV_VER=1.15
ZLIB_VER=1.2.8
PYTHON_WIN_VER=2.7.7
GMP=gmp-$GMP_VER
MPFR=mpfr-$MPFR_VER
MPC=mpc-$MPC_VER
ISL=isl-$ISL_VER
EXPAT=expat-$EXPAT_VER
LIBELF=libelf-$LIBELF_VER
LIBICONV=libiconv-$LIBICONV_VER
ZLIB=zlib-$ZLIB_VER
PYTHON_WIN=python-$PYTHON_WIN_VER
EXPAT_URL=https://downloads.sourceforge.net/project/expat/expat/$EXPAT_VER/$EXPAT_PACK
PYTHON_WIN_URL=https://www.python.org/ftp/python/$PYTHON_WIN_VER/$PYTHON_WIN_PACK
RELEASEVER=${release_year}-q1-update
RELEASEVER=${release_year}-q2-preview
RELEASEVER=${release_year}-q3-update
RELEASEVER=${release_year}-q4-major
GCC_VER=`cat $SRCDIR/$GCC/gcc/BASE-VER`
GCC_VER_NAME=`echo $GCC_VER | cut -d'.' -f1`
GCC_VER_SHORT=$GCC_VER_NAME
PKGVERSION="GNU Arm Embedded Toolchain $GCC_VER_NAME-$RELEASEVER"
PACKAGE_NAME=gcc-$TARGET-$GCC_VER_NAME-$RELEASEVER
INSTALL_PACKAGE_NAME=gcc-$TARGET-$GCC_VER_NAME-$RELEASEVER
APPNAME="$PKGVERSION $GCC_VER_SHORT $release_year"
```