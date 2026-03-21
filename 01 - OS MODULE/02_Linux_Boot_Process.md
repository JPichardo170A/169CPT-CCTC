# Linux Boot Process

> Reference: <https://os.cybbh.io/public/os/latest/007_linux_boot_process/linboot_fg.html>

## Step-by-Step Narrative

### 1. Power-On and Firmware Initialization

The system powers on, and the BIOS or UEFI firmware begins its job.

- Performs a Power-On Self Test (POST) to verify hardware.
- Locates a bootable device and reads the Master Boot Record (MBR) or GUID Partition Table (GPT).

### 2. First-Stage Bootloader

The first-stage bootloader (MBR or GPT) is loaded.

- MBR contains partition info and bootstrap code.
- GPT is used with UEFI and supports larger disks and more partitions.
- The bootstrap code loads GRUB Stage 1.

### 3. GRUB Stage 2

GRUB Stage 1 loads GRUB Stage 2.

- GRUB Stage 2 resides in the active partition (often `/boot`).
- It reads `grub.cfg` and displays a menu of available kernels.

### 4. Kernel and initrd Loading

GRUB loads the selected Linux kernel and initrd/initramfs.

- The kernel image (e.g., `/boot/vmlinuz-...`) is loaded into memory.
- initrd/initramfs provides a temporary root filesystem for early boot tasks.

### 5. Kernel Initialization

The Linux kernel takes control.

- Initializes hardware, memory, and CPU scheduling.
- Mounts the real root filesystem.
- Executes the first user-space process: `/sbin/init`.

### 6. Init System (SysV or systemd)

The init system begins.

- **SysV** reads `/etc/inittab` and executes scripts in `/etc/rc*.d` based on runlevel.
- **systemd** uses unit files (e.g., `graphical.target`, `multi-user.target`) to start services.

### 7. Services and Daemons

System services and daemons are launched.

- Networking, display manager, and login services start.
- For systemd, dependencies are managed via `Wants=` and `Requires=` in unit files.

### 8. Login Process

The login process begins.

- SysV uses `getty` to prompt for login, which invokes `/bin/login`.
- systemd starts `display-manager.service` (e.g., GDM) for graphical login.

### 9. User Environment

The user environment is prepared.

- Environment variables are loaded from `/etc/environment`, `/etc/profile`, `.bash_profile`, and `.bashrc`.
- These files customize the shell and user experience.

### 10. System Ready

The system is now fully booted and ready for user interaction.

- Users can launch applications, access the terminal, or use the graphical desktop.

---

## User Subsystems in Linux

Linux does not use sessions like Windows, but here is a breakdown of user-mode components:

### System Services (Daemons)

- `sshd` -- Secure Shell Daemon for remote access.
- `NetworkManager` -- Manages network connections.
- `cron` -- Schedules recurring tasks.
- `dbus` -- Message bus system for inter-process communication.

### Init Systems

- **SysV Init**
  - Uses runlevels (0-6) defined in `/etc/inittab`.
  - Scripts in `/etc/rc*.d` start/stop services.
- **systemd**
  - Uses targets like `graphical.target`, `multi-user.target`.
  - Unit files define services and dependencies.
  - `systemctl` manages and queries units.

### User Environment

- `.bashrc` and `.bash_profile` configure shell behavior.
- `/etc/environment` sets global variables like `PATH`.

---

## Boot Process Diagram

```
├── 1. Firmware Initialization (BIOS/UEFI)
│   ├── Performs POST (Power-On Self Test)
│   ├── Detects bootable device (HDD/SSD)
│   └── Loads MBR (Legacy BIOS) or GPT (UEFI)
│
├── 2. Bootloader Stage
│   ├── MBR/GPT points to GRUB Stage 1
│   ├── GRUB Stage 1 loads GRUB Stage 2
│   └── GRUB Stage 2 reads grub.cfg and displays boot menu
│
├── 3. Kernel Loading
│   ├── GRUB loads selected kernel (vmlinuz-*)
│   ├── Loads initrd/initramfs (temporary root filesystem)
│   └── Passes control to the Linux kernel
│
├── 4. Kernel Initialization
│   ├── Initializes CPU, memory, and hardware drivers
│   ├── Mounts real root filesystem
│   └── Executes first user-space process: /sbin/init
│
├── 5. Init System (SysV or systemd)
│   ├── SysV: Reads /etc/inittab and executes rc scripts
│   ├── systemd: Loads unit files and targets (e.g., multi-user.target)
│   └── Starts essential services and daemons
│
├── 6. Login and User Environment
│   ├── Starts getty or display-manager for login
│   ├── User logs in via shell or graphical interface
│   ├── Loads environment files (.bash_profile, .bashrc)
│   └── Launches desktop session or terminal
│
└── 7. System Ready
    ├── User can interact with shell or GUI
    ├── Applications and services are fully operational
    └── System is now in a usable state
```

---

## Trivia: GRUB and systemd

**Q: What does GRUB do?**
A: Loads the Linux kernel and initrd/initramfs.

**Q: What is the default systemd target for a graphical desktop?**
A: `graphical.target`

**Q: How does systemd know what services to start?**
A: Through `Wants=` and `Requires=` directives in unit files.

**Q: What command lists systemd dependencies?**
A: `systemctl list-dependencies graphical.target`
