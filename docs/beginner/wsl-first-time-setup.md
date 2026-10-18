# First-time setup: WSL and Podman

This quick reference covers installing Ubuntu in Windows Subsystem for Linux (WSL), then installing Podman in Ubuntu and using it to build and run the Beginner Boost container.

For more information about the Boost project, see [rwxrob/boost](https://github.com/rwxrob/boost).

## WSL commands

Run these commands from PowerShell or Windows Terminal, not from inside Ubuntu or the Boost container.

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

## Podman commands

Run these commands from your Ubuntu WSL shell, outside the Boost container.

### Install Podman

```bash
sudo apt update
sudo apt -y install podman
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
