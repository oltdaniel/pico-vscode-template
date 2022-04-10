# `pico-vscode-template`

This projects delivers you a full vscode setup with two ways of toolchain setups, toolchains folder without installation and systemw-ide installed tools. Additionally debugging will be done via a picoprobe setup.

> **NOTE**: This setup and template only targets Linux/Unix Systems. C/C++ Development for non-Windows targets on a Windows System is hell in reality. Something that should not be done.

## Setup

First create your own repository with this setup [via `Use this template`](https://github.com/oltdaniel/pico-vscode-template/generate). Then we do some general setup of the repository:

```bash
# first clone your repo
git clone git@github.com:username/pico.git
cd pico
# then we setup the pico-sdk submodules
git submodule update --init
# then we load the pico-sdk submodules (like tinyusb)
cd pico-sdk
git submodule update --init
```

Now the code part of the project is ready to go.

### Setup Picoprobe

Now we still need to flash picoprobe. Instructions can be found at the [Raspberry PI Pico Documentation](https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html#debugging-using-another-raspberry-pi-pico) and the UF2 can be found there too. [`picoprobe.uf2`](https://datasheets.raspberrypi.com/soft/picoprobe.uf2).

### Setup IDE

Before we continue, we want to make sure we have VSCode up and running with the correct extensions. For that you open the extension tab and search for `@recommended` and install all the necessary extensions.

Now you need to deice between two toolchain setups.

1. [Setup with system tools. Everything installed system-wide.](#setup-with-system-tools)
2. [Setup with toolchains folder. Everything installed in the toolchains folder.](#setup-with-toolchains-folder)

### Setup with system tools

Now you need to decide whether you have already installed the tools and compilers or whether you want them right in your project. Make sure you have the following packages installed:

- `arm-none-eabi-gcc` for the compiler relevant binaries and libraries.
- `arm-none-eabi-binutils` for tools like `objdump`.
- `arm-none-eabi-gdb` for the platform specific gdb binary.

As the release of openocd still misses the `picoprobe` we need to compile that by ourselves. The following dependencies have been listed to be required:

> **NOTE**: These are debian dependencies. If you use another distro, you probably know how to "translate" (find) the correct package names.

- `automake`
- `autoconf`
- `build-essential`
- `texinfo`
- `libtool`
- `libftdi-dev`
- `libusb-1.0-0-dev`

Simply follow this instruction:

```bash
# as we want to install it system-wide, we don't care where we are right now
# clone the openocd version
git clone https://github.com/raspberrypi/openocd.git --recursive --branch rp2040 --depth=1
cd openocd
# prepare the compile process
./bootstrap
# configure the project
# we want ftdi and picorpobe to work and disable compiler errirs (C standards <3)
./configure --enable-ftdi --enable-picoprobe --disable-werror
# now compile it (change 24 to the number of threads you have)
make -j24
# now we want to install it into the os directories
sudo make install
```

Now the toolchain is completly setup. Now simply change a single line in the CMake file `CMakeLists.txt` and we are ready to go:

```diff
- include(toolchains/gcc-arm-none-eabi_toolchain.cmake)
+ # include(toolchains/gcc-arm-none-eabi_toolchain.cmake)
```

We can now simply go back to VSCode and Select the correct Debug Task. Simply swicth to the Debug tab and select `Debug (system tools)` and press F5 to start the debugging.

### Setup with toolchains folder

If you don't want to install the toolchains system-wide, you can also setup this easily via the toolchains folder. Just follow this setup:

```bash
# download and extract the toolchain tools
cd toolchains
wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/10.3-2021.10/gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2
tar -xf gcc-arm-none-eabi-10.3-2021.10-x86_64-linux.tar.bz2
```

Now we still need to compile openocd. As the release of openocd still misses the `picoprobe` we need to compile that by ourselves. The following dependencies have been listed to be required:

> **NOTE**: These are debian dependencies. If you use another distro, you probably know how to "translate" (find) the correct package names.

- `automake`
- `autoconf`
- `build-essential`
- `texinfo`
- `libtool`
- `libftdi-dev`
- `libusb-1.0-0-dev`

Simply follow this instruction:

```bash
cd toolchains
# clone the openocd version
git clone https://github.com/raspberrypi/openocd.git --recursive --branch rp2040 --depth=1
cd openocd
# prepare the compile process
./bootstrap
# configure the project
# we want ftdi and picorpobe to work and disable compiler errirs (C standards <3)
./configure --enable-ftdi --enable-picoprobe --disable-werror
# now compile it (change 24 to the number of threads you have)
make -j24
```

We can now simply go back to VSCode and Select the correct Debug Task. Simply swicth to the Debug tab and select `Debug (toolchains folder)` and press F5 to start the debugging.

## Further reading

If you are already interested in debugging a Raspberry PI Pico, you are probably interested in the Chip architecture and lower level functions. See the following references:

- [**Getting Started Guide**](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf)
- [**Datasheet RP2040**](https://datasheets.raspberrypi.com/pico/pico-datasheet.pdf)
- [**pico-sdk PDF**](https://datasheets.raspberrypi.com/pico/raspberry-pi-pico-c-sdk.pdf)
- [**ARMv6-M Reference**](https://documentation-service.arm.com/static/5f8ff05ef86e16515cdbf826)

Maybe you are also interested into remote debugging with VSCode and the Raspberry PI Pico. For that you can checkout my other repo [rp2040-remote](https://github.com/oltdaniel/rp2040-remote). If you are interested in scheduler development, you can checkout [picos](https://github.com/oltdaniel/picos).

## License

_I don't really care. Happy learning and using this template._

[MIT License](./LICENSE)
