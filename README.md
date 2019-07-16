# firecracker-kernel
Build a Linux kernel and modules for AWS Firecracker
## Build Instructions
### Makefile
Edit the makefile and set the desired version. Then type make. This should build the kernel and the modules.
### Manual
```bash
BUILD_ARGS=-j8
LINUX_VER=4.19.59
curl -o linux.tar.xz -L https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-${LINUX_VER}.tar.gz
tar xfv linux.tar.xz
cp kernel.config linux-${LINUX_VER}/.config
cd linux-${LINUX_VER}

# Run the command 'make menu-config' for interactive configuration or
# the command './scripts/kconfig/merge_config.sh .config' for non-interactive configuration
./scripts/kconfig/merge_config.sh .config

# Build the kernel
make ${BUILD_ARGS} bzImage

# Build the modules and install in the present working directory (optional)
INSTALL_MOD_PATH=$PWD
make ${BUILD_ARGS} modules
make modules_install
mv lib/modules modules
tar -cvz modules > modules-${LINUX_VER}.tgz
cp -v .config ../config-${LINUX_VER} (This should go to /boot)

# Extract the compressed kernel
cd ..
./extract-vmlinux linux-${LINUX_VER}/arch/x86/boot/bzImage > vmlinuz-${LINUX_VER}
