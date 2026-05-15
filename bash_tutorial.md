# Bash and Environment Setup

This document covers opening a terminal, basic bash commands, and setting up the Python environment for the Cartopy tutorial.

---

## 1. What Is the Terminal?

The **terminal** (also called the command line or shell) is a text-based interface to your computer. Instead of clicking icons, you type commands. This tutorial uses **bash** — a widely used shell language that works the same across Mac, Linux, and Windows (with the right setup).

### Linux

Press `Ctrl + Alt + T`, or search for "Terminal" in your apps. Bash is the default — nothing extra needed.

### Mac

Press `Cmd + Space`, type `Terminal`, and press Enter. The default shell on modern Macs is `zsh`, but all commands in this tutorial are compatible with it. If you want to explicitly switch to bash, run:

```bash
bash
```

Your prompt will change to `bash-x.x$`.

### Windows — Installing Git Bash

Windows' built-in terminals (`cmd`, `PowerShell`) use a different command syntax. The easiest fix is to install **Git Bash**, which gives you a bash terminal on Windows.

1. Go to [https://git-scm.com/downloads](https://git-scm.com/downloads) and download the Windows installer.
2. Run the installer. When asked about the default editor and PATH, the defaults are fine — just keep clicking **Next**.
3. At the step "Adjusting your PATH environment", select **"Git from the command line and also from 3rd-party software"**.
4. Finish the installation.
5. Open **Git Bash** from the Start menu. You should see a prompt ending in `$`.

All commands in this tutorial should be run inside Git Bash, not in `cmd` or `PowerShell`.

---

Once your terminal is open, you will see a prompt like:

```
username@computer:~$
```

The `$` means the terminal is ready. You type after it and press **Enter** to run a command.

---

## 2. Paths, Root, and Home

Your files are organized in a tree of folders. A **path** is the address of a file or folder in that tree, written as names separated by `/`.

The **root directory** is the top of the tree, written as `/`. Everything on your system lives inside it.

The **home directory** is your personal folder, usually `/home/username` on Linux or `/Users/username` on Mac. The terminal shortens it to `~`, so `~/Documents` means `/Users/username/Documents`.

When you open a terminal, you start in your home directory. You can confirm this with:

```bash
pwd   # prints something like /Users/username
```

Paths can be written two ways:

- **Absolute**: starts from root, e.g. `/Users/username/project/map.py`
- **Relative**: starts from where you currently are, e.g. `project/map.py` or `../other_folder`

---

## 3. Essential Commands

```bash
ls          # list files and folders in the current directory
ls -lh      # same, but with file sizes in human-readable format
pwd         # print the full path of where you are
cd folder   # move into a folder
cd ..       # go up one level
cd ~        # go to your home directory
mkdir name  # create a new folder called "name"
rm file     # remove a file — permanent, no trash bin
```

> **Warning:** `rm` is irreversible. There is no undo. Be especially careful with `rm *`, which deletes everything in the current folder.

---

## 4. Configuration: `.bash_profile`

When bash starts, it reads a hidden file called `.bash_profile` in your home directory. This is where you can set environment variables, customize your prompt, or add folders to your PATH. You will rarely need to edit it manually, but it is useful to know it exists. Files starting with `.` are hidden — use `ls -a` to see them.

---

## 5. What Is Pixi?

**Pixi** is a package manager — a tool that installs Python and all the libraries you need, in an isolated environment, so nothing conflicts with other projects on your computer.

Think of it like a clean room dedicated to this project.

---

## 6. Installing Pixi

Run the command for your operating system in the terminal:

**Mac / Linux:**
```bash
curl -fsSL https://pixi.sh/install.sh | bash
```

**Windows (Git Bash):**
```bash
iwr -useb https://pixi.sh/install.ps1 | iex
```

The installer places pixi in `~/.pixi/bin` and should add it to your PATH automatically. If after reopening the terminal `pixi --version` fails, add it manually depending on your OS:

**Mac (bash):**
```bash
echo 'export PATH="$HOME/.pixi/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```

**Linux:**
```bash
echo 'export PATH="$HOME/.pixi/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

**Windows (Git Bash):**
```bash
echo 'export PATH="$HOME/.pixi/bin:$PATH"' >> ~/.bash_profile
source ~/.bash_profile
```

After installation, **close and reopen your terminal**. Then verify it worked:

```bash
pixi --version
```

You should see something like `pixi 0.x.x`.
---

## 7. The Project File: `pixi.toml`

A `pixi.toml` file tells Pixi everything about your project: its name, which packages to install, and which operating systems it supports.

Create a file called `pixi.toml` with the following content (we will place it in the right folder in the next step):

```toml
[workspace]
authors = []
channels = ["conda-forge"]
name = "cartopy_tutorial"
platforms = ["linux-64", "osx-arm64", "osx-64", "win-64"]
version = "0.1.0"

[tasks]

[dependencies]
cartopy = ">=0.25.0,<0.26"
numpy = ">=2.4.4,<3"
matplotlib = ">=3.10.9,<4"
xarray = ">=2026.4.0,<2027"
pandas = ">=3.0.3,<4"
jupyter = ">=1.1.1,<2"
```

---

## 8. Installing the Environment

Start by going to your home directory and creating a folder for this tutorial:

```bash
cd ~
mkdir cartopy_tutorial
cd cartopy_tutorial
```

Now place your `pixi.toml` file inside this folder. To confirm you are in the right place:

```bash
pwd   # should print something like /Users/username/cartopy_tutorial
ls    # should show pixi.toml
```

As a quick navigation exercise — go back home and return:

```bash
cd ..                # goes up one level, back to home
pwd                  # confirms you are in ~
cd cartopy_tutorial  # go back in
```

Then run:

```bash
pixi install
```

Pixi will download and install everything. This may take a few minutes the first time.

To open a shell inside the environment:

```bash
pixi shell
```

Your prompt will change slightly, indicating you are now inside the project environment. All Python commands from this point run with the installed packages.

To exit the environment:

```bash
exit
```