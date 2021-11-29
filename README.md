# Odroid N2+ Linux
A repo to house my linux kernel build code and setup instructions. As well as be an affordable web host for my rendered kernels.

# Compatibility
My kernel build(s) are based upon the Ubuntu Server 20.04 available at http://docs.linuxfactory.or.kr/install/prebuilt_image.html . And the kernel fork was sourced from https://github.com/chewitt/linux/tree/amlogic-5.15.y  (which is forked from Torvalds).

# Instructions
01. Download all of three .deb files
02. sudo dpkg -i *.deb
03. You should NOT see any errors
04. Reboot
05. Upon your new bootup, you should see that you are now running linux kernel 5.15
