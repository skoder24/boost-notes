# Windows, WezTerm, WSL2, and Podman containers

This note explains how Windows, WezTerm, Windows Subsystem for Linux (WSL2), Podman, and Linux containers can fit together. The exact arrangement depends on where Podman is installed and which Podman connection your commands use, so the diagram is a common Windows setup rather than a claim that every computer has the same stack.

## The short version

Windows is the host operating system. WezTerm is a terminal application running on Windows; it displays a shell session but is not another operating-system layer. WSL2 runs Linux distributions using a Linux kernel in a lightweight virtual machine managed by Windows. Podman needs a Linux environment to run Linux containers: on Windows, its `podman machine` commonly uses its own WSL2 distribution. A container is then an isolated set of Linux processes, files, and networking running on that Linux environment's kernel.

## Two common Podman layouts

### Windows Podman client with a Podman machine

When Podman is installed on Windows and you run `podman` from PowerShell, the Windows command is a client. It connects to the Podman service inside a machine, where the Linux containers actually run. The default Windows machine provider is WSL2; Hyper-V is another supported provider.

```text
Windows host
├── WezTerm (terminal window and input/output)
│   └── PowerShell (or another shell)
│       └── Windows podman client ─────────────┐
└── WSL2-managed Linux environment             │
    └── Podman machine distribution ◀─────────┘
        └── Podman service and OCI runtime
            └── Container processes
                └── Container filesystem and network namespace
```

The Podman machine is a separate WSL2 distribution, not normally a virtual machine nested inside your everyday Ubuntu or Debian WSL distribution. WSL2 distributions share the WSL2 Linux kernel and some virtual-machine resources, while each distribution has its own filesystem and Linux namespaces. The Podman machine has its own Podman storage, including its images and containers.

### Podman installed directly inside your WSL distribution

Another arrangement is to install Linux Podman inside your everyday WSL distribution and run its Linux engine there. In this arrangement, the Linux distribution is the container host; a separate Podman machine is generally not needed.

```text
Windows host
└── WezTerm
    └── WSL2 shell (for example, Ubuntu)
        └── Linux Podman CLI and engine
            └── OCI runtime
                └── Container processes and isolated resources
```

The command name `podman` alone does not tell you which arrangement is active. Its location, configuration, and selected system connection determine which Podman service it controls. A WSL shell can also be configured to use a remote Podman service, so check the connection rather than inferring it from the prompt.

## What each part does

| Part | Role | What it can access or control |
|---|---|---|
| **Windows** | Host operating system and desktop. | Runs WezTerm and can start WSL commands. Windows applications can access WSL files through supported paths, subject to permissions. |
| **WezTerm** | Terminal emulator and graphical interface. | Sends keystrokes to a shell and displays its output. It does not itself put the shell or container inside a security boundary. |
| **WSL2** | Linux environment and kernel integration on Windows. | Runs Linux distributions and Linux tools. WSL supports interoperability with Windows commands and files; access depends on paths, permissions, and configuration. |
| **Podman CLI** | Client used to ask a Podman service to manage containers. | Controls the Podman service selected by its local or remote connection configuration. The client may run in Windows or Linux. |
| **Podman machine** | Linux host environment used by Windows or macOS Podman installations. | Runs the Podman service and stores the images, containers, and volumes managed by that service. On Windows, the machine can use WSL2 or Hyper-V. |
| **OCI container** | Isolated process environment created by the container engine. | Sees its own filesystem and network setup plus resources deliberately made available to it. It does not get unrestricted access to the host merely because it runs there. |

## How can one part reach another?

### WezTerm and clickable links

WezTerm can scan text displayed in the terminal and recognize URLs as clickable links. If a process in a container prints `https://example.com`, that text travels through the container's output, the Podman/terminal session, and into WezTerm's display. Clicking the link asks the WezTerm application to open it. When WezTerm is running as a Windows application, the configured link action can open a Windows browser.

That is a user-interface handoff, not a process escaping the container. The browser is opened by the terminal application after it sees the printed text. Shift-click is how your setup activates the link; the exact mouse gesture depends on WezTerm's configuration. A process in the container making its own network request would be a separate event.

### Windows, WSL, and files

Windows and WSL are designed to interoperate. From Linux in WSL, Windows drives are commonly available under paths such as `/mnt/c`, and WSL can launch Windows programs when interoperability is enabled. From Windows, File Explorer can browse WSL files through `\\wsl.localhost\<distribution>\...` (older documentation may show `\\wsl$`). A WSL distribution's Linux filesystem is not the same filesystem as the Podman machine's distribution.

Microsoft recommends keeping Linux command-line projects in the WSL Linux filesystem for performance when working primarily from Linux. Files on a Windows drive can be convenient for Windows tools, but crossing filesystem boundaries can change performance and filesystem behavior.

For a container to see files from its Linux host, those files generally need to be passed into the container, for example with a bind mount or volume. With a remote Podman service or a Podman machine, host-path sharing also depends on the connection and file-sharing support between the client and machine. Do not assume a path visible in PowerShell or your WSL home directory automatically exists inside the container.

### Networking

Containers commonly have outbound network access, subject to the container network, machine, host firewall, and network policy. This is why a container can often download packages or contact a web service even though it cannot browse the host filesystem.

Inbound access to a service in a container is usually configured explicitly with a published port, such as `-p 8080:80`. Port publishing creates a route from a host-side port to a container port; it does not publish every container port or guarantee that a service is reachable from every network. WSL networking mode and Podman machine forwarding affect how Windows, WSL, and containers address each other. In particular, `localhost` means the current network context and does not universally refer to the Windows host from inside a container.

### Container isolation and its limits

A typical Linux container has a separate process tree, filesystem view, and network namespace, but it shares the Linux kernel of its container host. It is not a complete virtual machine with its own kernel. Linux namespaces, cgroups, capabilities, seccomp, and filesystem mounts provide different parts of the isolation; the exact protections depend on engine settings and the container configuration.

Isolation can be weakened intentionally or accidentally. A host-directory mount gives the container access to that mounted data, and options such as `--privileged` grant substantially broader access. Avoid mounting sensitive directories or exposing services unless needed. Containers are also not automatically network-disconnected: outbound networking is normal in many configurations.

Container data is not automatically permanent or automatically erased. A container that is stopped can usually be started again, while removing it removes its writable container layer. A volume or bind-mounted directory can keep data separately, and images and volumes are stored by the Podman service that owns them. Removing a Podman machine is a different, larger operation that can remove the storage managed by that machine.

## Check which setup you are using

Run these from Windows PowerShell if the Windows Podman CLI is installed:

```powershell
wsl --list --verbose
podman machine list
podman system connection list
podman info
```

Run these from the WSL distribution where you normally type `podman`:

```sh
command -v podman
podman system connection list
podman info
```

The WSL list shows installed distributions and their WSL versions. The Podman machine list shows machines managed by that Podman CLI, and the system connection list shows which Podman service a connection targets. `podman info` reports details about the service currently selected. Compare results from Windows and WSL if you use both; their `podman` commands may control different services with different images, containers, and volumes.

## Further reading

- [What is Windows Subsystem for Linux?](https://learn.microsoft.com/en-us/windows/wsl/about) — WSL2 architecture and Linux distribution behavior.
- [Working across file systems with WSL](https://learn.microsoft.com/en-us/windows/wsl/filesystems) — Windows/WSL file paths, interoperability, and performance.
- [Accessing network applications with WSL](https://learn.microsoft.com/en-us/windows/wsl/networking) — WSL networking modes and host-to-WSL access.
- [Podman for Windows](https://github.com/podman-container-tools/podman/blob/main/docs/tutorials/podman-for-windows.md) — Windows installation, providers, and Podman machines.
- [`podman machine`](https://docs.podman.io/en/latest/markdown/podman-machine.1.html) — managing Podman's Linux machine.
- [`podman run`](https://docs.podman.io/en/latest/markdown/podman-run.1.html) — container process, filesystem, networking, mounts, and port options.
- [WezTerm hyperlinks](https://wezterm.org/hyperlinks.html) — implicit and explicit clickable links in terminal output.
