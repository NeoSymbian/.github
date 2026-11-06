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
- ✅ The full EKA2 kernel (flexible memory model) boots on QEMU and on the phone, on one core so far: kernel threads,
  the system tick and timers, and exception traps.
- ✅ Rust runs on the 2010 Symbian user side (QEMU Syborg): EXEs written in Rust only, making kernel exec calls
  directly, with exit codes and panics ([e32-rs](https://github.com/NeoSymbian/e32-rs)).
- ✅ A Rust launcher UI renders through Slint's software renderer ([neoui](https://github.com/NeoSymbian/neoui)).
- ⏳ Next: the full kernel on all 8 cores, then user mode and a Symbian shell on the phone; on the Rust side, memory,
  threads and IPC.

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
