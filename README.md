# ansible-role-handbrake-cli
Overview

This role prepares a RHEL-based system to compile and run HandBrakeCLI, including optional NVIDIA NVENC GPU acceleration.
It installs development toolchains, multimedia codec libraries, CUDA components, and environment variables required for a successful build.

This role is intended for homelab or media-server nodes where automated video transcoding is needed.

## Task-by-Task Breakdown
Installing Development Tools

This task installs the compiler toolchain and build essentials required to compile HandBrake from source.
Packages such as GCC, make, autoconf, and related developer utilities are included.
Without these tools, HandBrake cannot be built or linked properly, so this establishes the foundation for all later stages.

Installing Multimedia Codec Dependencies

This task installs the wide range of codec libraries HandBrake depends on.
This includes support for H.264, H.265, VP8, VP9, AAC, Vorbis, and others.
HandBrake's build system requires the corresponding -devel packages to link against codec implementations, otherwise certain formats or features would be missing.

Adding the NVIDIA CUDA Repository

This task configures the system to access NVIDIA’s official CUDA RPM repository.
Doing so makes GPU acceleration packages available through the native package manager.
This is essential for enabling NVENC functionality inside HandBrake, since CUDA headers and SDK components must be present at build time.

Installing CUDA Toolkit and GPU Support Packages

Once the repository is enabled, this task installs CUDA runtime libraries, NVENC-related components, and development headers.
This ensures the system provides both the driver userspace libraries and the CUDA compiler required for applications to target NVIDIA hardware.
HandBrake’s NVENC encoder requires these libraries during compilation.

Configuring CUDA Environment Variables

A script is placed into /etc/profile.d to export CUDA paths (bin and lib64).
This ensures tools such as nvcc are available from any shell session.
It also ensures the dynamic linker can locate CUDA runtime libraries without additional manual configuration.

Activating CUDA Environment in Current Session

The role immediately sources the new environment script so the running Ansible session has access to CUDA tools.
This avoids waiting for a system reboot to verify CUDA support.
It also guarantees the subsequent validation tasks can run successfully.

Validating the CUDA Compiler (nvcc)

This task runs nvcc --version to confirm the CUDA compiler is installed and functional.
A working nvcc indicates the toolchain is configured correctly and CUDA integration is usable.
If this validation fails, HandBrake will not be able to compile GPU-accelerated components.

Validating NVENC Runtime Libraries

The role confirms the presence of the NVENC/NVRTC shared libraries in the CUDA tree.
These files are required by HandBrake’s GPU encoder backend.
If missing, NVENC support will silently be disabled during compilation, so this serves as a final guardrail.

## Summary

This role completely prepares a system for GPU-accelerated HandBrakeCLI compilation.
It ensures the toolchain, codecs, CUDA dependencies, and environment variables are all correctly installed and validated.