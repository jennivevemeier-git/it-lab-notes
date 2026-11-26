# Lab note 01: My home lab setup

**Date:** 26 November 2026
**Status:** Completed (snapshot to be added)

## Goal
Build a safe virtual environment on my MacBook for practising Linux,
C programming and IT support skills. Windows and cloud labs follow later.

## Environment
| Item | Value |
|---|---|
| Host | MacBook M4 (Apple Silicon, ARM64) |
| Hypervisor | UTM (QEMU 10.0 ARM Virtual Machine) |
| Guest OS | Ubuntu 26.04.1 LTS "Resolute Raccoon", Desktop (ARM64) |
| Resources | 4 CPU cores, 8 GB RAM (7.2 GiB usable), 30 GB disk |
| Network | UTM Shared Network (virtio-net-pci), interface `enp0s1`, 192.168.64.3/24 |
| Hostname | `jenni-ubuntu` |

## Steps
1. Downloaded the Ubuntu ARM64 image from ubuntu.com
   (Apple Silicon needs ARM64, x86 images do not run well).
2. Created the VM in UTM with 4 cores, 8 GB RAM and a 30 GB disk.
3. Installed Ubuntu and created a user with sudo rights.
4. Removed the installer ISO from the virtual CD/DVD drive.
5. Verified the system with `cat /etc/os-release`, `uname -m`, `nproc`,
   `free -h`, `df -h /` and `ip -br a` (screenshot below).
6. Wrote and compiled small C programs with `vim` and `cc`.
7. Fixed the VM clock (see Problems and fixes).
8. Documented the setup in this GitHub repository.

## Screenshots
![System information](screenshots/vm-system-info.png)
![chrony tracking](screenshots/chrony-tracking.png)

## Problems and fixes

- **Symptom:** Grey window although the VM status was "Started".
- **Cause / fix:** [write what solved it, e.g. waited for boot / clicked into the window]
- **Lesson:** A running VM and a visible display are separate things.

### 2. VM clock was 12 days behind (14 Nov instead of 26 Nov)
- **Symptom:** Wrong date in the top bar. `timedatectl` showed
  "System clock synchronized: no".
- **Diagnosis:** System clock wrong, hardware clock (RTC) had the correct date.
- **Quick fix:** `sudo hwclock --hctosys` and
  `sudo timedatectl set-timezone Europe/Zurich`.
- **Dead end:** `systemctl restart systemd-timesyncd` failed with
  "Unit not found". This Ubuntu uses **chrony** for time sync.
- **Real fix:** chrony synchronised with Ubuntu's NTP servers and
  corrected a remaining offset of 609 seconds.
- **Verified with:** `chronyc tracking` (Leap status: Normal) and
  `chronyc sources` (reach 377, selected source marked `^*`).
- **Lesson:** Check which service exists before restarting it. A wrong
  clock can break HTTPS, `apt` and Kerberos logins.

### 3. C compile and link errors
| Message | Meaning | Fix |
|---|---|---|
| `undefined reference to 'main'` | Linker error: no entry point | Add a `main()` function |
| `expected expression before '}' token` | Compiler (syntax) error | Check the line above the reported one |
| `character.c: No such file or directory` | Typo: the file was created as `character-c` | `mv character-c character.c` |
- **Lesson:** Compiler errors appear before the program is built, linker
  errors after it.

### 4. Typos in commands
- `gif` instead of `git`, `timedatect1` instead of `timedatectl`,
  `chronic` instead of `chronyc`.
- **Lesson:** Read the suggestion in the error message before installing
  anything. `chronic` would have installed an unneeded package (moreutils).

### 5. Ran VM commands on the Mac
- **Symptom:** `/etc/os-release: No such file`, `nproc: command not found`.
- **Cause:** The commands were run in the macOS terminal, not the VM.
- **Lesson:** Check the prompt (`user@host`) before running system commands.

## What I learned
- Apple Silicon needs ARM64 images.
- How to compare the system clock, hardware clock and sync status
  with `timedatectl`.
- The difference between compiler and linker errors.
- How to read error messages and verify a fix with a command.
- A VM and its host are separate machines for Git, files and commands.

## Next steps
- [ ] Create a UTM snapshot: `01-clean-install-updated`
- [ ] Wireshark capture on the virtual network (lab note 02)
- [ ] Windows 11 ARM VM
- [ ] Windows Server / Active Directory via an Azure free account
