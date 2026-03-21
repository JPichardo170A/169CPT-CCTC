# Windows NT 6 Boot Process

> Reference: <https://os.cybbh.io/public/os/latest/006_windows_boot_process/winboot_fg.html>

## Step-by-Step Narrative

### 1. Power-On and Firmware Initialization

The first thing that happens is the system powers on, and the BIOS or UEFI firmware begins its job.

- It performs a Power-On Self Test (POST) to check hardware.
- Then it looks for a bootable device, like a hard drive or SSD.

### 2. Master Boot Record (MBR)

Once a bootable device is found, the firmware reads the Master Boot Record (MBR).

- The MBR contains information about the disk partitions and points to the volume boot sector.

### 3. Volume Boot Sector

The volume boot sector is loaded next.

- This small piece of code locates and launches the Windows Boot Manager -- a file called `BOOTMGR`.

### 4. BOOTMGR

`BOOTMGR` now takes control.

- It reads the Boot Configuration Data (BCD), which contains information about installed operating systems.
- If multiple OS entries exist, `BOOTMGR` displays a menu to let the user choose.
- Once a selection is made (or automatically chosen), `BOOTMGR` loads the Windows Boot Loader -- `winload.exe`.

### 5. winload.exe

`winload.exe` begins loading the core components of Windows.

- It loads the Windows kernel (`ntoskrnl.exe`), the Hardware Abstraction Layer (`hal.dll`), and essential boot drivers.
- It also loads the system registry hive and sets up paging files like `pagefile.sys`.

### 6. Windows Kernel (ntoskrnl.exe)

After `winload.exe` finishes, control is handed to the Windows kernel -- `ntoskrnl.exe`.

- The kernel initializes core subsystems like memory management, process scheduling, and security.
- This is the transition from booting to running the operating system.

### 7. Session Manager Subsystem (smss.exe)

Next, the Session Manager Subsystem (`smss.exe`) is launched.

- `smss.exe` loads additional registry hives, initializes paging, and starts `csrss.exe` -- the Client/Server Runtime Subsystem.
- `csrss.exe` is responsible for managing user-mode processes and console windows.

### 8. wininit.exe

Then, `wininit.exe` is started. It launches critical services:

- `lsass.exe` (Local Security Authority) -- handles authentication and security.
- `services.exe` -- starts system services.
- `lsm.exe` -- manages sessions and remote logins.

### 9. winlogon.exe

After system services are running, `winlogon.exe` is launched.

- `winlogon.exe` handles user logon and authentication.
- It starts `userinit.exe`, which prepares the user environment.

### 10. Desktop Shell

Finally, `userinit.exe` launches `explorer.exe`.

- `explorer.exe` provides the graphical desktop, taskbar, and file manager.
- At this point, the system is fully booted and ready for user interaction.

---

## User Subsystems

This is the first part of Windows that a user is able to manipulate. The user subsystem manages all user applications like process creation, internet connectivity, and object access through API calls to `hal.dll`.

User Subsystems run in **Session 0** and **Session 1**:

```
├── Session 0
│   ├── System Services
│   │   ├── lsass.exe (Local Security Authority)
│   │   │   Handles user authentication, password validation, and security policies.
│   │   ├── services.exe (Service Control Manager)
│   │   │   Manages system services like networking, printing, and background tasks.
│   │   └── svchost.exe (Service Host Containers)
│   │       Hosts multiple Windows services in shared processes to optimize resource use.
│   └── Core OS Processes
│       ├── wininit.exe
│       │   Initializes critical system services after the kernel loads.
│       ├── smss.exe (Session Manager)
│       │   Starts user sessions, initializes system environment, and launches csrss.exe.
│       └── csrss.exe (Client/Server Runtime Subsystem)
│           Manages console windows, thread creation, and some user-mode functions.
│
├── Session 1
│   ├── Winlogon Process
│   │   ├── winlogon.exe
│   │   │   Handles secure user logon, Ctrl+Alt+Del, and session setup.
│   │   └── userinit.exe
│   │       Prepares the user environment and launches the desktop shell.
│   │       └── explorer.exe (Desktop Shell)
│   │           Provides the graphical interface: desktop, taskbar, file explorer.
│   ├── User Applications
│   │   ├── Web Browsers
│   │   ├── Office Suites
│   │   ├── Games
│   │   └── Custom Software
│   └── API Access
│       ├── Kernel32.dll
│       │   Provides core system functions like memory management and I/O.
│       ├── User32.dll
│       │   Manages user interface elements like windows, buttons, and messages.
│       ├── GDI32.dll
│       │   Handles graphics rendering for 2D visuals and drawing operations.
│       └── hal.dll (via system calls)
│           Interfaces directly with hardware through the Hardware Abstraction Layer.
```

---

## Trivia: BCDEdit

**Q: How many Boot Managers are present and how many should there be?**
A: There is only one and there should be only one.

**Q: What would be indicated if more than one Boot Loader was present?**
A: More than one boot loader present would indicate a dual boot system.

**Q: What does the identifier `{current}` indicate?**
A: This is the default boot loader which would load if no user selection was made if multiple boot loaders were present.
