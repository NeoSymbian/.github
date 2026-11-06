# NeoSymbian

A hobby project bringing **open-source Symbian** back to modern hardware. It uses EKA2, the Symbian^3 kernel released
in 2010.

The first target is the **Google Pixel 3a XL**:
- the EKA2 kernel ported to **64-bit ARM (AArch64)**;
- the original 2010 Symbian user side running unchanged in 32-bit mode;
- later, a new touch UI written in **Rust**.

## Status

- ✅ The 2010 SMP nanokernel boots on QEMU and on the **real phone**, on all 8 cores (GICv3, generic timer, PSCI, SMP
  load balancing).
- ✅ The standalone nanokernel test suite (nkernsa) completes on the phone, with results drawn on its screen.
- ✅ The full EKA2 kernel (flexible memory model) boots on QEMU and on the phone, on all 8 cores: kernel threads,
  the system tick and timers, and exception traps.
- ✅ User mode on the phone: 32-bit ARM Symbian code runs on the 64-bit kernel and makes system calls. The kernel
  emulates the old instructions ARMv8 dropped (SWP, misaligned LDRD/LDM).
- ✅ The real 2010 user side boots on the phone: the file server and estart with a RAM drive as C:, and the Rust
  test programs pass there (heap, timers, async, threads, semaphores, files).
- ✅ The Symbian shell (eshell) on the phone screen, drawn by the original 2010 window server.
- ✅ Rust runs on the 2010 Symbian user side (QEMU Syborg): EXEs written in Rust only, making kernel exec calls
  directly, with a heap, timers, async, threads and a Rust client for the file server
  ([e32-rs](https://github.com/NeoSymbian/e32-rs)).
- ✅ IPC servers in Rust: a Rust server process serves an unmodified C++ Symbian client (`RSessionBase`) and Rust
  clients, the groundwork for the Rust UI server.
- ✅ A Rust launcher UI renders through Slint's software renderer ([neoui](https://github.com/NeoSymbian/neoui)).
- ✅ The Rust launcher **replaces the window server** on the 2010 Symbian user side (QEMU Syborg): it draws straight
  to the frame buffer and takes raw touch and key events from the kernel; tapping Phone opens the Phone screen.
- ✅ Apps run as **separate Symbian processes** under the Rust UI server (QEMU Syborg): a live Clock app draws into
  a shared surface, gets touches, and exits on Back.
- ⏳ Next: input on the phone (touch, keys), then the Rust UI on the phone; on the Rust side, a terminal for the old
  text programs (R6b).

Follow along in the [roadmap](https://github.com/NeoSymbian/symbian-pixel/blob/main/docs/roadmap.md).

## Repositories

| Repository | What |
|---|---|
| [symbian-pixel](https://github.com/NeoSymbian/symbian-pixel) | New work: the AArch64 kernel port, the Pixel board support, docs and roadmap |
| [oss.FCL.sf.os.kernelhwsrv](https://github.com/NeoSymbian/oss.FCL.sf.os.kernelhwsrv) | The EKA2 kernel. Our changes to the original files are commits on `main`. |
| `oss.*` (98 more) | The rest of the 2010 Symbian source, forked from [SymbianSource](https://github.com/SymbianSource) |
| [core](https://github.com/NeoSymbian/core), [pdk](https://github.com/NeoSymbian/pdk) | The build system, forked from [Phorepoc](https://github.com/Phorepoc) |

In every fork, the original branches stay untouched and our work is on `main`. Nothing is pushed back upstream.

## Credits and licences

- Symbian source: Nokia and the Symbian Foundation, EPL-1.0, via [SymbianSource](https://github.com/SymbianSource).
- Build system and many fixes for modern compilers: [Phorepoc](https://github.com/Phorepoc), Apache-2.0.
- Our own code: Apache-2.0.

NeoSymbian is not affiliated with Nokia, Accenture, the former Symbian Foundation, or Google. "Symbian" is used only to
describe the software this project builds on.
