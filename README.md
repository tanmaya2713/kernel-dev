Motorola One Fusion+ (liber) Custom Kernel with KernelSU-Next
This repository contains the complete source code, configuration, and AnyKernel3 packaging setup to build and flash a custom, KernelSU-Next enabled kernel for the Motorola One Fusion+ (codename: liber) running Android 14.

Device Specifications
Device: Motorola One Fusion+ (liber)

Platform: Qualcomm Snapdragon 730 / SM6150

Kernel Version: Linux 4.14 (Non-GKI legacy architecture)

Boot Image Architecture: Header v2 (Requires uncompressed raw Image)

Root Solution: KernelSU-Next (integrated via Kprobes)

Repository Structure
Plaintext
/kernel-dev
├── kernel/                  # Kernel source tree with integrated KernelSU-Next & SELinux patches
├── anykernel3/              # AnyKernel3 packaging scripts and tools for flashable ZIP creation
└── .gitignore               # Excludes build artifacts (out/, Image, zips) to keep repo clean
Step-by-Step Setup & Build Guide
Prerequisites
Make sure your build environment (such as a GitHub Codespace or a Linux workstation) has the required build tools installed:

make, gcc, bc, flex, bison, libssl-dev, git, zip

Appropriate ARM64 toolchains (AArch64 GCC/Clang)

Step 1: Clone the Repository
Clone this repository with all its contents:

Bash
git clone https://github.com/tanmaya2713/kernel-dev.git
cd kernel-dev
Step 2: Configure the Kernel
Navigate into the kernel source tree and load your device defconfig:

Bash
cd kernel

# Set environment variables for ARM64 compilation
export ARCH=arm64
export SUBARCH=arm64

# Load the defconfig for liber
make liber_defconfig
Step 3: Build the Kernel
Compile the kernel using your toolchain. For header v2 devices like the Motorola One Fusion+, the build system generates an uncompressed Image file:

Bash
make -j$(nproc) O=out
Once the compilation finishes successfully, your outputs will be located at:

out/arch/arm64/boot/Image (Primary uncompressed kernel binary required for liber)

Packaging with AnyKernel3
AnyKernel3 allows you to flash your custom kernel safely via custom recoveries like TWRP or OrangeFox without messing up your ramdisk structure.

Step 1: Copy the Kernel Image
Copy your compiled uncompressed Image into the AnyKernel3 directory:

Bash
cd /workspaces/kernel-dev
cp kernel/out/arch/arm64/boot/Image anykernel3/
Step 2: Generate the Flashable ZIP
Navigate to the anykernel3 folder, clean up metadata, and compress the package:

Bash
cd anykernel3
rm -rf .git
zip -r9 ../Motorola-One-Fusion-Plus-KernelSU-Next-Uncompressed.zip *
Your flashable zip is now generated at:
../Motorola-One-Fusion-Plus-KernelSU-Next-Uncompressed.zip

Flashing Instructions
Transfer the ZIP: Copy Motorola-One-Fusion-Plus-KernelSU-Next-Uncompressed.zip from your PC/Codespace to your phone's internal storage or an SD card.

Boot into Recovery: Power off your device and boot into your custom recovery (TWRP or OrangeFox).

Flash the ZIP:

Tap on Install.

Select Motorola-One-Fusion-Plus-KernelSU-Next-Uncompressed.zip.

Swipe to confirm the flash. (AnyKernel3 will automatically parse your v2 boot header and patch the boot partition).

Reboot: Clear cache/dalvik (optional) and reboot to system.

Verification
Once booted into Android 14, install the official KernelSU-Next Manager APK.

Open the app to verify that KernelSU-Next is active and operational.

Key Developer Notes & Fixes Applied
SELinux Pointer Cast Fix: Fixed type mismatches in drivers/kernelsu/selinux/sepolicy.c by correctly casting flex_array pointers to avtab_node to ensure successful compilation on Linux 4.14.

Uncompressed Boot Image: Motorola One Fusion+ expects a raw uncompressed Image rather than a compressed Image.gz, handled natively by the custom AnyKernel3 configuration.
