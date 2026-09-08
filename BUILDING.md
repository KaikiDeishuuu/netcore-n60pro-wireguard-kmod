# Reproducible build outline

The build must use the exact patched vendor tree and target configuration. A
plain `linux-5.4.284` tree is not ABI-compatible.

## Inputs

- Ubuntu or WSL Ubuntu build host
- Vendor repository at commit
  `02fff2aebcfe5f4e2166ebc8697e9edf932c5a77`
- `wireguard-linux-compat-v1.0.20220627`
- `config/config-5.4.284` from this repository
- OpenWrt-generated GCC 8.4 toolchain for `aarch64_cortex-a53_musl`

## Prepare the patched kernel tree

```sh
git clone -b openwrt-21.02 \
  https://github.com/padavanonly/immortalwrt-mt798x.git immortalwrt-mt798x
cd immortalwrt-mt798x
git checkout 02fff2aebcfe5f4e2166ebc8697e9edf932c5a77

cat > .config <<'EOF'
CONFIG_TARGET_mediatek=y
CONFIG_TARGET_mediatek_mt7986=y
CONFIG_TARGET_mediatek_mt7986_DEVICE_netcore_n60-pro=y
EOF

make defconfig
FORCE_UNSAFE_CONFIGURE=1 make -j"$(nproc)" toolchain/install
make -j"$(nproc)" target/linux/prepare
```

Locate the patched kernel tree and prepare it with the supplied configuration:

```sh
KDIR="$(find build_dir -maxdepth 5 -type d -name linux-5.4.284 | head -1)"
cp ../config/config-5.4.284 "$KDIR/.config"

TOOLCHAIN="$PWD/staging_dir/toolchain-aarch64_cortex-a53_gcc-8.4.0_musl/bin"
CROSS="$TOOLCHAIN/aarch64-openwrt-linux-"

make -C "$KDIR" ARCH=arm64 CROSS_COMPILE="$CROSS" olddefconfig
make -C "$KDIR" ARCH=arm64 CROSS_COMPILE="$CROSS" -j"$(nproc)" modules_prepare
```

## Build WireGuard

From the `src` directory of `wireguard-linux-compat-v1.0.20220627`:

```sh
make clean
make ARCH=arm64 CROSS_COMPILE="$CROSS" KERNELDIR="$KDIR" module
```

## Build the combined UDP tunnel module

Create a module directory with this `Kbuild`:

```make
obj-m := udp_tunnel.o
udp_tunnel-y := udp_tunnel4.o udp_tunnel6.o
```

Copy the source from the patched kernel tree and build it:

```sh
cp "$KDIR/net/ipv4/udp_tunnel.c" udp_tunnel4.c
cp "$KDIR/net/ipv6/ip6_udp_tunnel.c" udp_tunnel6.c
make -C "$KDIR" M="$PWD" ARCH=arm64 CROSS_COMPILE="$CROSS" modules
```

## Required checks

Both modules must be ARM aarch64 relocatable ELF files and must report exactly:

```text
5.4.284 SMP mod_unload aarch64
```

The formal IPKs additionally depend on exactly:

```text
kernel (=5.4.284-1-e9cf91df0a4fdfa42b1117918a7e0f50)
```

The source tree's default package ABI may differ when its complete historical
firmware configuration is unavailable. Do not install an IPK whose kernel ABI
dependency differs from the target.
