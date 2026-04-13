# uiscsi

Pure-userspace iSCSI in Go. No kernel modules, no open-iscsi, no iscsi-initiator-utils. Import the library and talk to iSCSI targets directly from your Go application.

## The Stack

```
tapeplayer          TUI FLAC player for iSCSI tape drives
uiscsi-tools        CLI tools (uiscsi-ls, uiscsi-tape-dd)
    |                   |
    +-------+-----------+
            |
       uiscsi-tape      SSC tape driver (Read, Write, Rewind, Space, ...)
            |
          uiscsi         RFC 7143 iSCSI initiator library (stdlib-only)
```

```
tapesim-tcmu        TCMU-based SSC tape emulator (SCSICmdHandler for 13 SSC/SPC commands)
    |         |
 tapesim    go-tcmu     In-memory tape sim / Go TCMU kernel interface
```

## Repositories

| Repo | What it does |
|------|-------------|
| **[uiscsi](https://github.com/uiscsi/uiscsi)** | iSCSI initiator library. Full RFC 7143: login negotiation, CHAP, CmdSN windowing, error recovery (ERL 0/1/2), streaming I/O. Stdlib-only. |
| **[uiscsi-tape](https://github.com/uiscsi/uiscsi-tape)** | SSC-3 tape driver over iSCSI. `tape.Open` gives you a `Drive` with Read, Write, WriteFilemarks, Rewind, Position, SetBlockSize, SetCompression. |
| **[tapeplayer](https://github.com/uiscsi/tapeplayer)** | TUI audio player that reads FLAC from iSCSI tape drives. Playlist navigation, LRU data cache, streaming playback on slow drives. |
| **[uiscsi-tools](https://github.com/uiscsi/uiscsi-tools)** | CLI tools: `uiscsi-ls` (target/LUN discovery), `uiscsi-tape-dd` (tape data transfer). |
| **[go-tcmu](https://github.com/uiscsi/go-tcmu)** | Go bindings for the Linux TCM Userspace (TCMU) kernel API. Build SCSI target devices in pure Go. |
| **[tapesim](https://github.com/uiscsi/tapesim)** | In-memory SSC-3 tape simulation. Shared tape state machine for testing and emulation. |
| **[tapesim-tcmu](https://github.com/uiscsi/tapesim-tcmu)** | TCMU tape emulator. Implements all 13 SSC/SPC commands a tape driver issues. Used for kernel-path E2E testing. |

## Use Cases

**Access iSCSI storage from Go without kernel dependencies.** Import `uiscsi`, dial a target, execute SCSI commands. Works on any platform with TCP -- no kernel iSCSI stack required.

**Drive tape over iSCSI.** Import `uiscsi-tape` for a high-level tape API. Supports LTO and DDS drives, variable and fixed block modes, streaming I/O for large transfers.

**Build userspace SCSI targets.** Import `go-tcmu` to create SCSI devices backed by Go code. The kernel presents your handler as a real SCSI device accessible over iSCSI, vHost, or any LIO fabric.

**Test tape software without hardware.** `tapesim` + `tapesim-tcmu` create a virtual tape drive visible to the kernel. Write E2E tests that exercise the full iSCSI + SCSI + tape path.

## Standards

- RFC 7143 (iSCSI)
- SSC-3 (SCSI Stream Commands -- tape)
- SPC-4 (SCSI Primary Commands -- sense data, inquiry)

## License

All repositories are Free Software under the GNU General Public License v3.0.
