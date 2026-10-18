# First-time setup: WSL and Podman

This quick reference covers installing Ubuntu in Windows Subsystem for Linux (WSL), then installing Podman in Ubuntu and using it to build and run the Beginner Boost container.

For more information about the Boost project, see [rwxrob/boost](https://github.com/rwxrob/boost).

## WSL commands

Run these commands from PowerShell or Windows Terminal. Optional: WezTerm.

### Install Ubuntu in WSL

```powershell
wsl --install -d Ubuntu
```

The `-d Ubuntu` option selects the Ubuntu distribution. Restart Windows if prompted, then launch Ubuntu and follow the prompts to create a Linux username and password.

### List installed distributions

```powershell
wsl --list --verbose
```

This shows installed distributions and whether they are running, along with their WSL version. The shorter equivalent is:

```powershell
wsl -l -v
```

### Delete a distribution

```powershell
wsl --unregister Ubuntu
```

**Warning:** This permanently deletes the Ubuntu distribution, including its files, installed software, settings, and projects stored inside it. Back up anything you need before running this command. It does not uninstall WSL itself.

## Ubuntu shell commands

Run these commands at the Ubuntu prompt in WSL. Ubuntu uses Bash as its default shell, so these are commonly called **shell commands** or **Linux commands**. They are not WSL management commands; WSL commands such as `wsl --list` are run from Windows.

### Work with packages using `apt`

For interactive command-line use, Ubuntu documents `apt` as the high-level package-management interface intended for end users. `apt-get` is also valid, but is a lower-level interface traditionally preferred in scripts for its more stable behavior. The command is spelled `apt-get` with a hyphen; `sudo apt get` is not the equivalent. See the Ubuntu [`apt` manual](https://manpages.ubuntu.com/manpages/noble/en/man8/apt.8.html) and [`apt-get` manual](https://manpages.ubuntu.com/manpages/noble/en/man8/apt-get.8.html).

Refresh the package information available from configured software sources:

```bash
sudo apt update
```

Install available upgrades for packages already installed:

```bash
sudo apt upgrade
```

Install a package by name. For example, install `tree`:

```bash
sudo apt install tree
```

Remove an installed package by name. This example removes `tree`:

```bash
sudo apt remove tree
```

Read the proposed changes before confirming an install, upgrade, or removal. `remove` uninstalls the package but may leave its system configuration files; it does not remove files in your personal home directory.

### Navigate and inspect files

Print the current working directory:

```bash
pwd
```

List files and directories in the current directory:

```bash
ls
```

Show a detailed listing, including permissions, owner, size, and modification time:

```bash
ls -l
```

Show files beneath the current directory as a directory tree. Install `tree` first using `sudo apt install tree` if it is not already installed:

```bash
tree
```

Change to another directory. `cd ..` moves to the parent directory; `cd ~` returns to your home directory:

```bash
cd ..
cd ~
```

## Podman commands

Run these commands from your Ubuntu WSL shell, outside the Boost container.

### Install Podman

```bash
sudo apt update
sudo apt install podman
```

### Build the Boost image

```bash
podman build -t boost https://github.com/rwxrob/boost.git
```

This downloads the Boost repository as the build context and builds an image named `boost`.

### Create and start a container

```bash
podman run -it --hostname boost --name boost boost
```

This creates a container named `boost` from the `boost` image and opens an interactive terminal in it.

### Start and attach to an existing container

```bash
podman start -ai boost
```

Use this when the container already exists but is stopped. The `-a` flag attaches to it, and `-i` keeps standard input open for interactive use.

### Delete a container

```bash
podman rm -f boost
```

**Warning:** This forcibly removes the container named `boost`. Data saved only inside that container will be lost. This does not remove the image; use `podman rmi boost` only if you also intend to delete the image.
