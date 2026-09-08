# Release notes

## `v1.0.20220627-n60pro.2`

Final package revision: `5.4.284-4`.

### Package hardening

- Refuses live installation unless the board is `netcore,n60`.
- Refuses live installation unless the firmware revision is
  `r20651+1-02fff2aebc` and the running kernel is `5.4.284`.
- Retains the exact opkg kernel ABI dependency.
- Adds `SourceDateEpoch` provenance metadata.
- The combined UDP tunnel package now declares `Replaces` as well as
  `Provides` and `Conflicts` for the split OpenWrt UDP tunnel packages.
- `Installed-Size` is generated from the deterministic compressed data archive
  by OpenWrt's `ipkg-build` and is verified after packaging.

### Artifacts

- Combined IPv4/IPv6 UDP tunnel module: 9,144 bytes
- WireGuard module: 173,080 bytes
- Both modules use vermagic `5.4.284 SMP mod_unload aarch64`
- Module and autoload files use mode `0644`
- Package maintainer scripts use mode `0755`

### Router acceptance

The GCC 8.4 modules were installed and tested across a controlled reboot:

- both modules autoloaded in the required order;
- WireGuard 1.0.20220627 loaded without symbol errors;
- `wg0` returned `UP` and `LOWER_UP`;
- an active peer resumed handshakes and traffic;
- no Oops, Panic, Call trace, or WireGuard load error was observed;
- the dedicated WireGuard firewall zone remained operational.

Package revision 4 changes package guards and metadata only. Its two module
binaries are byte-identical to the reboot-tested revision 2 and 3 binaries.

### Known scope

These files have only been validated on the exact firmware and ABI documented
in the README. They are not generic MT7986 or generic Linux 5.4.284 modules.
