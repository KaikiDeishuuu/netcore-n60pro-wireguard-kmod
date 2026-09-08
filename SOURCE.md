# Corresponding source and provenance

The release artifacts are built entirely from GPL-licensed sources.

## Vendor OpenWrt/kernel tree

- Repository: <https://github.com/padavanonly/immortalwrt-mt798x>
- Branch: `openwrt-21.02`
- Exact commit: `02fff2aebcfe5f4e2166ebc8697e9edf932c5a77`

This tree contains the OpenWrt build system, Linux 5.4 patches, MediaTek target
patches, and GCC 8.4 toolchain recipes used for this build.

## WireGuard compat source

- Project: <https://git.zx2c4.com/wireguard-linux-compat/>
- Snapshot/tag: `v1.0.20220627`

## Build configuration and packaging

The exact kernel configuration used for `modules_prepare` is included at
`config/config-5.4.284`. Package metadata and module-loader files are included
under `packaging/`. See `BUILDING.md` for the commands and toolchain identity.

The binary packages are provided without warranty. Redistributors must comply
with the licenses of the Linux kernel, WireGuard, OpenWrt, and any other source
components they redistribute.
