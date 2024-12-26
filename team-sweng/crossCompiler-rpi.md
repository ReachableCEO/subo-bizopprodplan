CNW

05/27/2021 at 10:49

<https://deardevices.com/2019/04/18/how-to-crosscompile-raspi/>

git clone <https://github.com/raspberrypi/tools>
killed that

found a better option (uses docker so it's nicely contained)

<https://github.com/Pro/raspi-toolchain>

realized that might be (only for) rpi0 dug into source material and found

<https://solarianprogrammer.com/2018/05/06/building-gcc-cross-compiler-raspberry-pi/>

Light modifications to the instructions (--werror and path adjustments):

```console
../glibc-2.28/configure --prefix=/subo/Tooling/bin/team-sweng/cross-compile/rpi/arm-linux-gnueabihf --build=$MACHTYPE --host=arm-linux-gnueabihf --target=arm-linux-gnueabihf --with-arch=armv6 --with-fpu=vfp --with-float=hard --with-headers=/subo/Tooling/bin/team-sweng/cross-compile/rpi/arm-linux-gnueabihf/include --disable-multilib libc_cv_forced_unwind=yes --disable-werror
install csu/crt1.o csu/crti.o csu/crtn.o /subo/Tooling/bin/team-sweng/cross-compile/rpi/arm-linux-gnueabihf/lib
arm-linux-gnueabihf-gcc -nostdlib -nostartfiles -shared -x c /dev/null -o /subo/Tooling/bin/team-sweng/cross-compile/rpi/arm-linux-gnueabihf/lib/libc.so
touch /subo/Tooling/bin/team-sweng/cross-compile/rpi/arm-linux-gnueabihf/include/gnu/stubs.h
```

Glibc build well underway as of 17:58 on 05/28/2021

Executable built on buildbox , scp to my pi, run. Patient closed at 18:27 on 05/28/2021

```console

root@buildbox:~# arm-linux-gnueabihf-gcc hello-pi.c -o hello-pi
root@buildbox:~# file hello-pi
hello-pi: ELF 32-bit LSB shared object, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux-armhf.so.3, BuildID[sha1]=ee4a21a234906299467b7e0ec735d400497c5ad8, for GNU/Linux 3.2.0, not stripped
> ./hello-pi
Hello rpi cross!%                                                                                                                                                                             ~                                                                                                                                                                                  18:26:12
```