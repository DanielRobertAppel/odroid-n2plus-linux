# Odroid N2+ Linux
A repo to house my linux kernel build code and setup instructions. As well as be an affordable web host for my rendered kernels.

# Compatibility
My kernel build(s) are based upon the Ubuntu Server 20.04 available at http://docs.linuxfactory.or.kr/install/prebuilt_image.html . And the kernel fork was sourced from https://github.com/chewitt/linux/tree/amlogic-5.15.y  (which is forked from Torvalds).

# How I do it
## 00. Caveats
01. I build on the native device.  No cross-compilation used.  I was simply too scared that adding a twist like cross-compilation would be yet another hole for me to fall into.  So I made the decision to spend WAAAY longer compiling on-device, than get the quick compilation on a different architecture.
02. I use an emmc module for storage on my N2Plus.  I don't know if using an SD Card makes any difference at all, so, if it does... you've kinda been warned.

## 01. Initial tools needed
`sudo apt install git build-essential bc flex bison libssl-dev libncurses-dev libelf-dev`
### 01a. Optional tools
`sudo apt install nano tmux rsync`

## 02. Clone
_For Example: If you were wanting to make a 5.15 version build_
`git clone -b amlogic-5.15.y --depth 1 https://github.com/chewitt/linux`

## 03. Configure
```bash
cd linux
# We are going to copy the preset configuration which was used to build the 5.13 kernel on the N2 .  That way, all we have to do is to configure all of the optional modules which were made available since that build was released.
cp /boot/config-5.13.0-odroid-arm64 .config
# This next command allows us to set our new kernel's build name.  This is very important, I'll explain later
sed -i "s/.*CONFIG_LOCALVERSION_AUTO.*/CONFIG_LOCALVERSION_AUTO=n/g" .config
# I don't actually think that this next instruction does anything at all, but it is at least harmless
echo "-odroid-n2-plus-arm64" > .scmversion
# Get ready to answer some yes/no questions about 'if this/that module/feature should be built/included'
make -j 6 LOCALVERSION="-odroid-n2-plus-arm64" oldconf
# Whew, that took some time, Let's get building.
make -j 6 LOCALVERSION="-odroid-n2-plus-arm64" modules amlogic/meson-g12b-odroid-n2-plus.dtb
# Prep the kernel flashing
sudo sed -i "s/^force=.*$/force=\"yes\"/g" /usr/share/flash-kernel/functions
# Prep the modules and the existing module libraries
sudo ln -s $PWD/arch/arm64/boot/dts /usr/lib/linux-image-$(cat include/config/kernel.release)
# Install the new modules
sudo make -j 6 LOCALVERSION="-odroid-n2-plus-arm64" modules_install
# Build the Kernel
make LOCALVERSION="-odroid-n2-plus-arm64"
# Install your new Kernel
sudo make install
```

## 04. Packaging
If you want to redistribute your new kernel/modules/tweaks , then you can pack it all up very neatly with:
```bash
nice make -j 6 LOCALVERSION="-odroid-n2-plus-arm64" oldconfig modules amlogic/meson-g12b-odroid-n2-plus.dtb bindeb-pkg
```
