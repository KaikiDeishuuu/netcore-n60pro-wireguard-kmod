# WireGuard kernel modules for Netcore N60 PRO

Vendor-kernel-compatible WireGuard modules for one specific ImmortalWrt build
on the Netcore N60 PRO (MediaTek MT7986).

## Compatibility

These packages are **only** for the following target:

- Device: Netcore N60 PRO
- Architecture: `aarch64_cortex-a53`
- Firmware: `ImmortalWrt 21.02-SNAPSHOT r20651+1-02fff2aebc`
- Kernel: `5.4.284`
- Kernel package ABI: `5.4.284-1-e9cf91df0a4fdfa42b1117918a7e0f50`
- Module vermagic: `5.4.284 SMP mod_unload aarch64`

> [!CAUTION]
> Do not force-install these modules on another firmware build, even if its
> visible kernel version is also 5.4.284. The vendor tree changes core network
> structure layouts. An ABI mismatch can crash and reboot the router when a
> WireGuard interface is created.

## Release packages

Install the two packages from `packages/` in this order:

1. `kmod-udptunnel-combined_5.4.284-3_aarch64_cortex-a53.ipk`
2. `kmod-wireguard_5.4.284-3_aarch64_cortex-a53.ipk`

Verify the files before installation:

```sh
sha256sum -c SHA256SUMS
```

Then copy them to the router and install both in one transaction:

```sh
opkg install \
  /tmp/kmod-udptunnel-combined_5.4.284-3_aarch64_cortex-a53.ipk \
  /tmp/kmod-wireguard_5.4.284-3_aarch64_cortex-a53.ipk
reboot
```

Use a local maintenance connection and keep a recovery path available. Back up
the existing modules and `/etc/config/network` plus `/etc/config/firewall`
before replacing any kernel module.

## Why the ordinary upstream kernel headers are unsafe

The target firmware is built from a patched MediaTek/OpenWrt kernel tree. A
layout probe produced:

| Tree | `sizeof(net_device)` | `nd_net` offset | private offset | `sizeof(net_device_ops)` |
|---|---:|---:|---:|---:|
| Vanilla Linux 5.4.284 | 2048 | 1184 | 2048 | 528 |
| Patched vendor tree | 2112 | 1248 | 2112 | 544 |

This is why a module compiled against vanilla 5.4.284 can have matching
vermagic and still crash inside `register_netdevice()`.

## Build provenance

- Vendor source: [`padavanonly/immortalwrt-mt798x`](https://github.com/padavanonly/immortalwrt-mt798x)
- Branch: `openwrt-21.02`
- Commit: `02fff2aebcfe5f4e2166ebc8697e9edf932c5a77`
- WireGuard compat source: `wireguard-linux-compat-v1.0.20220627`
- Compiler: `aarch64-openwrt-linux-gcc (OpenWrt GCC 8.4.0 r0-02fff2a) 8.4.0`

See [BUILDING.md](BUILDING.md) for the reproducible build outline and
[RELEASE-NOTES.md](RELEASE-NOTES.md) for validation details.

## Repository contents

- `packages/`: final OpenWrt IPKs
- `modules/`: release `.ko` files for inspection or recovery
- `packaging/`: IPK control files and module-loader entries
- `config/`: target kernel configuration used for module preparation
- `SHA256SUMS`: checksums for release artifacts

## License

WireGuard and the Linux kernel components are licensed under GPL-2.0. See
[LICENSE](LICENSE) and [SOURCE.md](SOURCE.md). No warranty is provided.
