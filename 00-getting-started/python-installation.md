# Python Installation - Revision Notes ~ sm

> **In one line:** To run Python programs you must install the **Python interpreter** (the program that reads and runs your code). After installing, check it from a terminal with `python --version` (Windows) or `python3 --version` (macOS and Linux).

## Table of Contents
- [What is the Python Interpreter?](#what-is-the-python-interpreter)
- [Which Version Should I Install?](#which-version-should-i-install)
- [Install on Windows](#install-on-windows)
- [Install on macOS](#install-on-macos)
- [Install on Linux](#install-on-linux)
- [Check That Python Works](#check-that-python-works)
- [python vs python3 vs py](#python-vs-python3-vs-py)
- [Editors to Write Code In](#editors-to-write-code-in)
- [Python 2 vs Python 3](#python-2-vs-python-3)

---

## What is the Python Interpreter?

Python is the **language** you write. The **interpreter** is the program that reads your Python code and runs it. If the interpreter is not installed, your computer cannot run `.py` files.

| Term | Plain meaning |
|---|---|
| **Python** | The programming language |
| **Interpreter** | The program (`python`) that runs your code |
| **CPython** | The standard interpreter from python.org (the one these notes use) |
| **Terminal / command prompt** | A text window where you type commands |
| **Version** | Written as `major.minor.micro`, for example `3.15.1` |

> Downloading Python only needs to be done once. Every note in this repository assumes **Python 3**.

---

## Which Version Should I Install?

Python 3 gets a new minor version (3.14, 3.15, ...) about **once a year, in October**. Each version is supported for about five years.

Status of the versions (from the official Python Developer's Guide, checked on 24 Oct 2026):

| Version | Status | First release | End of life |
|---|---|---|---|
| 3.15 | bugfix | 2026-10-09 | 2031-10 (scheduled) |
| 3.14 | bugfix | 2025-10-07 | 2030-10 (scheduled) |
| 3.13 | security | 2024-10-07 | 2029-10 (scheduled) |
| 3.12 | security | 2023-10-02 | 2028-10 (scheduled) |
| 3.11 | security | 2022-10-24 | 2027-10 (scheduled) |
| 3.10 | end-of-life | 2021-10-04 | 2026-10-01 |

| Status | Meaning |
|---|---|
| **bugfix** | Gets bug fixes and security fixes |
| **security** | Gets security fixes only |
| **end-of-life** | No more updates; do not start new work on it |

- **Best choice:** the latest stable version shown on [python.org/downloads](https://www.python.org/downloads/) (at the time of writing, **3.15.1**).
- **Minimum for these notes:** Python **3.10**, because `match` / `case` (see the Match Case note) needs 3.10 or newer. Other parts need less: f-strings need 3.6+, and the `|` merge for dictionaries needs 3.9+.
- Dates can change. Always check the [status page](https://devguide.python.org/versions/) for the current table.

---

## Install on Windows

The official way on Windows is the **Python install manager**. It gives you the `python`, `py` and `pymanager` commands.

### 1. Get the install manager
Use any one of these (use only one: the official docs say the Store and python.org versions cannot be installed at the same time):
- **Microsoft Store:** search for "Python Install Manager" and click **Install**.
- **python.org:** open [python.org/downloads](https://www.python.org/downloads/), download the install manager file (`.msix`), double-click it and choose **Install**.
- **Command line (WinGet):**
```
winget install 9NQ7512CXL7T -e --accept-package-agreements --disable-interactivity
```

### 2. Install Python itself
If no Python is installed yet, simply typing `python` or `py` in a terminal installs the latest stable release for you. You can also do it yourself:
```
py install 3.15
```
Useful install-manager commands:

| Command | What it does |
|---|---|
| `py list` | Shows the Python versions installed on your computer |
| `py list --online` | Shows the versions you can install |
| `py install 3.15` | Installs Python 3.15 |
| `py help` | Lists all commands and options |

### 3. Check the install
Open **Command Prompt** or **PowerShell** and type:
```
python --version
```

### If `python` is not found (or opens the Microsoft Store)
1. Click **Start** and open **Manage app execution aliases**.
2. Make sure **Python (default)** is switched **on** (switching it off and on again can refresh it).
3. Check that `%UserProfile%\AppData\Local\Microsoft\WindowsApps` is in your `PATH`.

### The older full installer (`python-3.x.x-amd64.exe`)
- This traditional installer is **deprecated since Python 3.14**, and the docs say it will **not be produced for Python 3.16 and later**. Prefer the install manager.
- If you still use it, tick the option that **adds Python to `PATH`** ("Add python.exe to PATH"). Without it, typing `python` in a terminal will not work.

> **PATH** is the list of folders your terminal searches when you type a command. If Python's folder is not on it, the terminal says the command is not found.

---

## Install on macOS

1. Download the **Python for macOS** installer (a `.pkg` file) from [python.org/downloads](https://www.python.org/downloads/). It runs on both Apple Silicon and Intel Macs. Check the download page for the macOS versions it supports.
2. Double-click the `.pkg` file and click **Continue** through the Read Me and **Agree** to the license.
3. Keep the standard installation and click **Install**. You must enter the password of a user with **Administrator** rights.
4. When the summary window appears, open the `Python 3.15` folder inside **Applications** and double-click **Install Certificates.command**. It downloads the security certificates that Python needs for secure (HTTPS) connections. It is finished when the Terminal shows `Successfully installed certifi` and `update complete`.
5. Open **Terminal** and check:
```
python3 --version
```

What gets installed:
- **IDLE** and **Python Launcher** in `/Applications/Python 3.15/`
- The Python framework in `/Library/Frameworks/Python.framework`
- Links to Python in `/usr/local/bin/`

> macOS already contains an older, limited `/usr/bin/python3` that Apple uses for its own tools. Do **not** delete or change it. The python.org version works alongside it.

> The 3.15 installer also adds an optional **free-threaded** build, run with `python3.15t`. Beginners can ignore it.

---

## Install on Linux

Most Linux systems already include Python 3. Check first:
```
python3 --version
```

If it is missing, install it with your package manager:

| Distribution | Command |
|---|---|
| Debian / Ubuntu | `sudo apt update` then `sudo apt install python3 python3-pip python3-venv` |
| Fedora | `sudo dnf install python3` |
| Arch | `sudo pacman -S python` |

- The version you get depends on your distribution, so it may be older than the latest release. Python also runs fine from an older supported version.
- On Debian and Ubuntu, `pip` and `venv` are separate packages (`python3-pip` and `python3-venv`), which is why the command above installs them.
- Do **not** remove the system Python: parts of the operating system depend on it. For a newer version, build from source or use a tool that installs Python separately.
- You can also build Python from the source archive on [python.org/downloads](https://www.python.org/downloads/).

---

## Check That Python Works

```
python --version
```
Example output (your version number will differ):
```
Python 3.15.1
```
For more detail about the build, use a capital `-VV`:
```
python -VV
```

Also check that **pip** (the tool that installs extra packages) is available:
```
python -m pip --version
```

| What you see | What it means |
|---|---|
| `Python 3.x.x` | Python is installed correctly |
| `command not found` / `not recognized` | Python is not installed, or not on `PATH` (see the sections above) |
| Microsoft Store opens (Windows) | Fix the app execution alias (see Install on Windows) |

> On macOS and Linux, use `python3` in place of `python` in every command above.

---

## python vs python3 vs py

Different systems name the command differently.

| System | Command to use |
|---|---|
| Windows | `python` or `py` |
| macOS | `python3` |
| Linux | `python3` (the plain `python` command may not exist) |

- On Windows, `py` is the launcher that comes with the install manager. To choose a version, write `py -V:3.15` before the other options.
- This repository writes `python` in examples. Use `python3` instead if you are on macOS or Linux.

---

## Editors to Write Code In

You need a place to type your code. Any plain-text editor works, but these make life easier:

| Tool | Notes |
|---|---|
| **IDLE** | Python's own simple editor and shell. The python.org macOS installer includes it |
| **VS Code** | A free code editor. Install the official **Python** extension from Microsoft |
| **PyCharm** | A full Python IDE from JetBrains |
| Any text editor | Works, as long as you save the file with the `.py` extension |

> Do not write code in a word processor (Word, WordPad). It adds hidden formatting that Python cannot read.

---

## Python 2 vs Python 3

- You may see old tutorials that use **Python 2**. It has **not been supported since 1 January 2020**.
- Python 3 is **not** fully compatible with Python 2. For example, `print "Hi"` (Python 2) is an error in Python 3, which needs `print("Hi")`.
- Always learn and install **Python 3**.

---

- [Back to section index](./README.md)
