# Release notes

## `v1.0.20220627-n60pro.1`

Final package revision: `5.4.284-3`.

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

Package revision 3 only normalizes package file permissions; its two module
binaries are byte-identical to the reboot-tested revision 2 binaries.

### Known scope

These files have only been validated on the exact firmware and ABI documented
in the README. They are not generic MT7986 or generic Linux 5.4.284 modules.
