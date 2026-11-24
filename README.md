# crest-chroot

A high-performance, **ptrace-free** chroot and bind-mount emulation engine tailored specifically for [Crest-IDE](https://github.com/Crest-IDE/Crest-IDE) on Android (AArch64).

---

## ⚡ Features

- **Zero-Ptrace Overhead**: Eliminates PRoot's multi-process ptrace bottleneck. Intercepts path-bearing syscalls in-process via **seccomp `RET_TRAP` → `SIGSYS`** for near-native CPU, memory, and filesystem speed.
- **Freestanding Single Binary**: Compiled with `-static -nostdlib -no-pie`. Zero external dependencies — no `libc.so`, no `libtalloc.so`, and no Android dynamic linker namespace conflicts (~500 KB stripped).
- **Custom Userland ELF Loader (`ul_exec`)**: Bypasses Android's `noexec` restriction on app storage and SD cards by mapping guest ELF binaries into anonymous executable memory.
- **Android Rootless & SELinux Confined**: Runs out-of-the-box on Android 10+ (tested up to Android 15, Linux kernel 5.15/6.1+) without root or user namespaces.
- **Linux 6.1 Kernel Personality**: Synthesizes clean `/proc` (`version`, `cmdline`, `maps`, `mounts`), provides fake root identity (`-u` / `0:0`), and handles hardlinks via `--link2symlink`.

---

## 🛠️ Building

On Termux / Android native (or AArch64 Linux with `clang`):

```sh
make
```

Outputs:
- `build/crest-chroot` — Standalone static executable.
- `build/libcrest-chroot.so` — Packageable native library for Android APK `jniLibs`.

---

## 🚀 Usage

```sh
# Run a program inside a guest rootfs with fake root (0:0)
./build/crest-chroot -u /path/to/rootfs /bin/sh

# Bind host directories into guest paths (SRC:DST)
./build/crest-chroot -u -b /sdcard:/sdcard /path/to/rootfs /bin/bash

# Start in a specific guest working directory
./build/crest-chroot -u -w /root /path/to/rootfs /bin/sh -l

# Check device and kernel compatibility
./build/crest-chroot --probe
```

---

## 📜 License

Licensed under the [Apache License, Version 2.0](LICENSE).
Based on the upstream architecture of `chroot-ng` by Sylirre.
