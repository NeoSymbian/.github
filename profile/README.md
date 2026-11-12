[![NeoSymbian: open-source Symbian for modern hardware](https://raw.githubusercontent.com/NeoSymbian/.github/main/profile/assets/neo-symbian-banner.svg)](https://github.com/NeoSymbian/docs)

# NeoSymbian

**Open-source Symbian for modern hardware.** NeoSymbian is a volunteer, non-commercial project building on the Symbian^3 EKA2 source released in 2010.

The first target is the **Google Pixel 3a XL**. The EKA2 kernel runs on **64-bit ARM (AArch64)**, the original 2010 Symbian user side runs in 32-bit mode, and a new touch UI written in **Rust** runs on the phone.

[Documentation](https://github.com/NeoSymbian/docs) · [Roadmap](https://github.com/NeoSymbian/docs/blob/main/roadmap.md) · [Build system](https://github.com/NeoSymbian/build) · [Kernel](https://github.com/NeoSymbian/kernel) · [Rust UI](https://github.com/NeoSymbian/neoui)

## What works today

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
- ✅ Input on the phone: the power and volume keys and the touchscreen (Synaptics, over I2C), with kernel drivers
  that power up what the bootloader leaves off.
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
- ✅ The Rust UI server runs **on the phone** in place of the shell: it draws the launcher and takes taps.
- ✅ A **Terminal app** in the Rust UI runs ESHELL and the 2010 text programs (QEMU Syborg): the stock console DLL
  talks to a console server written in Rust. Taps are exact on any screen size (the emulator's pointer driver was
  fixed at the source).
- ✅ **Storage on the phone:** Symbian's own FAT32 file system on the eMMC: `C:` (8 GB, the system drive) and `E:`
  (~42 GB, mass memory) on the `userdata` partition, with long file names, TRIM and about 70 MB/s reads. Files
  survive a reboot and a forced reset. A USB debug console lets the phone be tested without anyone touching it.
- ✅ **NeoSymbian is installed on the phone and boots by itself.** The system drive `Z:` is read from flash, and
  every block is checked against SHA-256 hashes. A/B slots: a bad update falls back to the previous one on its own.
  A **Dev tools** app shows and saves the kernel's logs on the phone.
- ✅ **The Syborg emulator moved to QEMU 11.1** ([symbian-qemu](https://github.com/NeoSymbian/symbian-qemu)): the
  board rewritten in C with tests, runs natively on Apple Silicon too, VNC for live viewing, saved snapshots. Test
  runs wait for the serial log instead of fixed sleeps: the Rust test suite went from ~4 minutes to ~20 seconds.
- ✅ **A faster, steadier UI on the phone** ([smooth-ui](https://github.com/NeoSymbian/docs/blob/main/smooth-ui.md) S1–S3):
  no lost taps, frames paced at 60 fps, and the fast cores at full clock (300 → 1996 MHz) while the screen is
  touched, with the UI moved onto them. Dragging went from 8 to 37–62 frames per second. Animations now copy
  already-drawn pixels instead of redrawing: the home page swipe takes 6 ms a frame, and screen fades and list
  scrolling are 2–3× faster.
- ✅ **Cellular on the phone** ([telephony](https://github.com/NeoSymbian/docs/blob/main/telephony.md)): our kernel
  boots the Pixel's modem and runs its packet engine (IPA). A Rust daemon talks to the modem: it registers on
  **LTE**, the status bar shows the operator and signal bars, and calls go out and come in from the Phone screen
  with **two-way audio** on the earpiece or loudspeaker (Mute, Speaker, volume keys, keypad tones). The Pixel's
  audio DSP runs under our kernel.
- ✅ **Wi-Fi on the phone** ([wifi](https://github.com/NeoSymbian/docs/blob/main/wifi.md) W0–W4): the modem starts
  the Wi-Fi firmware, our kernel drives the WCN3990's copy engines, HTC and WMI, and **Settings → Wi-Fi lists the
  networks nearby** on 2.4 and 5 GHz. **The phone joins a WPA2 network**: our kernel does the 802.11 join, a Rust
  supplicant ([neolink](https://github.com/NeoSymbian/neolink) `wpa`) runs the key handshake, and the status bar shows
  the link. An IP address (DHCP) is next.
- ✅ **A system keyboard in three languages** ([neoui-shell](https://github.com/NeoSymbian/docs/blob/main/neoui-shell.md)):
  English, Armenian (phonetic) and Russian, switched with a globe key and managed in **Settings → Language &
  keyboard**. Any text field raises it, for example the Wi-Fi password. The Terminal gets an Esc, Tab and arrows row.

## Next

Complete calling (telephony C7) is being implemented on coordinated
`feat/c7-complete-calls` branches: ringing and vibration, multi-call controls, SIM/radio UI and
modem/audio-DSP recovery. Phone acceptance, including the documented return-to-LTE issue, remains pending.

Sharing `E:` with a PC over USB, updates from the phone itself, and power (battery, idle, sleep)
  ([the storage plan](https://github.com/NeoSymbian/docs/blob/main/filesystem.md)).

Follow along in the [roadmap](https://github.com/NeoSymbian/docs/blob/main/roadmap.md).

## Repositories

| Repository | What |
|---|---|
| [kernel](https://github.com/NeoSymbian/kernel) | The EKA2 kernel with the AArch64 port, drivers and boards (`baseport/arm64`), in Symbian's own layout |
| [build](https://github.com/NeoSymbian/build) | The build system: `neo`, the build image, the pinned sources, the 2010 ROM |
| [e32-rs](https://github.com/NeoSymbian/e32-rs), [neoui](https://github.com/NeoSymbian/neoui) | Rust on the Symbian user side, and the Rust UI server and apps |
| [docs](https://github.com/NeoSymbian/docs) | Roadmap, Rust integration, the port map |
| [telephony](https://github.com/NeoSymbian/telephony) | The cellular modem's user side in Rust: QRTR, QMI, the modem daemon |
| [neolink](https://github.com/NeoSymbian/neolink) | Wireless connectivity in Rust: the Wi-Fi firmware's control client and the WPA2 supplicant today, Bluetooth later |
| [symbian-qemu](https://github.com/NeoSymbian/symbian-qemu) | The Syborg board package: the baseport, QEMU 11.1 with the Syborg board, the test runner |
| `oss.*` | The 2010 Symbian packages the build pins, forked from [SymbianSource](https://github.com/SymbianSource) |

Archived: `symbian-pixel` (the port's first home), and the Phorepoc forks (`core`, `pdk`, `adaptation-*`) with the
`oss.*` packages only they used.

In every fork, the original branches stay untouched and our work is on `main`. Nothing is pushed back upstream.

## Credits and licences

- Symbian source: Nokia and the Symbian Foundation, EPL-1.0, via [SymbianSource](https://github.com/SymbianSource).
- The first build system, the x86-64 port template and many fixes for modern compilers: [Phorepoc](https://github.com/Phorepoc), Apache-2.0.
- Our own code: Apache-2.0.

NeoSymbian is not affiliated with Nokia, Accenture, the former Symbian Foundation, or Google. "Symbian" is used only to
describe the software this project builds on.
