# Virtual Environments - Revision Notes ~ sm

> **In one line:** A **virtual environment** (venv) is a **private folder with its own Python and its own installed packages** for one project. It keeps projects from interfering with each other. Create it with `python -m venv .venv`, then activate it.

## Table of Contents
- [Why Do We Need One?](#why-do-we-need-one)
- [Create a Virtual Environment](#create-a-virtual-environment)
- [Activate It](#activate-it)
- [Check That It is Active](#check-that-it-is-active)
- [Install Packages Inside It](#install-packages-inside-it)
- [Deactivate](#deactivate)
- [What is Inside the Folder?](#what-is-inside-the-folder)
- [Share and Recreate](#share-and-recreate)
- [Run Without Activating](#run-without-activating)
- [Useful Options](#useful-options)
- [Rules of Thumb](#rules-of-thumb)
- [Common Problems](#common-problems)
- [Workflow Summary](#workflow-summary)

---

## Why Do We Need One?

Imagine two projects:

| Project | Needs |
|---|---|
| Project A | `library` version 1.0 |
| Project B | `library` version 2.0 |

If both used the same global Python, only one version could be installed at a time, and one project would break. Also, installing many packages globally makes the system's Python messy.

A virtual environment gives **each project its own set of packages**.

| Benefit | Meaning |
|---|---|
| **Isolation** | Different projects can use different package versions |
| **Clean** | The global Python stays untouched |
| **Repeatable** | `requirements.txt` lists exactly what the project needs |
| **No admin rights** | Packages are installed inside your project folder |

---

## Create a Virtual Environment

`venv` is part of the standard library (on Debian and Ubuntu you may first need `sudo apt install python3-venv`).

Go to your project folder and run:
```
python -m venv .venv
```
(Use `python3` on macOS and Linux.) This creates a folder named `.venv`. The name is your choice, but `.venv` (or `venv`) is the common one.

---

## Activate It

Activating makes `python` and `pip` in your terminal use the environment.

| System / shell | Command |
|---|---|
| **Windows**, Command Prompt | `.venv\Scripts\activate.bat` |
| **Windows**, PowerShell | `.venv\Scripts\Activate.ps1` |
| **macOS / Linux**, bash or zsh | `source .venv/bin/activate` |

The terminal prompt then starts with the environment name:
```
(.venv) $
```

> **PowerShell error "running scripts is disabled on this system"?** Allow local scripts for your user once: `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. Command Prompt does not have this problem.

---

## Check That It is Active

```
which python           # macOS / Linux
where python           # Windows
```
It should show a path **inside** `.venv`, for example `/home/you/project/.venv/bin/python`.

From Python itself:
```python
import sys

print(sys.prefix != sys.base_prefix)
# Output: True   (when run inside an active virtual environment)
```
Outside a virtual environment this prints `False`.

---

## Install Packages Inside It

With the environment active, `pip` installs only into it:

```
python -m pip install six
python -m pip list
```
```
Package Version
------- -------
pip     26.2.2
six     1.16.0
```
A new environment is almost empty: only `pip` (and nothing else) is installed. The global packages are **not** visible.

---

## Deactivate

```
deactivate
```
The `(.venv)` prefix disappears and `python` is the global one again. The environment folder stays; activate it again whenever you work on the project.

---

## What is Inside the Folder?

```
.venv/
├── bin/ (Scripts\ on Windows)   python, pip, activate ...
├── lib/ (Lib\ on Windows)       site-packages/  <- installed packages
├── include/
└── pyvenv.cfg                   settings: base Python, version
```
`pyvenv.cfg` records which Python the environment was made from, for example:
```
home = /usr/local/bin
include-system-site-packages = false
version = 3.13.17
```
The `python` inside `.venv` is a link or small copy that points to the original interpreter, so a virtual environment is small compared to a full Python install.

---

## Share and Recreate

**Never copy or commit the `.venv` folder.** It contains paths specific to your computer and can be hundreds of megabytes. Share the **list of packages** instead.

1. On your computer, save the list:
```
python -m pip freeze > requirements.txt
```
2. Add `.venv/` to `.gitignore`. (Python 3.13 and newer create a `.gitignore` inside the environment automatically.)
3. On another computer, rebuild it:
```
python -m venv .venv
source .venv/bin/activate          # Windows: .venv\Scripts\activate
python -m pip install -r requirements.txt
```
A virtual environment cannot be moved to another folder. Delete it and create a new one.

---

## Run Without Activating

You can call the environment's Python directly. This is handy in scripts and editors.

```
.venv/bin/python app.py            # macOS / Linux
.venv\Scripts\python.exe app.py    # Windows
```
It uses the environment's packages even though it was never activated.

---

## Useful Options

| Option | Meaning |
|---|---|
| `python -m venv .venv` | Create |
| `--system-site-packages` | Also let the environment see the global packages |
| `--clear` | Empty an existing environment before creating it again |
| `--upgrade-deps` | Upgrade pip to the latest version while creating |
| `--prompt NAME` | Show a different name in the prompt |
| `--without-pip` | Do not install pip |

To delete an environment, deactivate it and delete the folder (`.venv`). Nothing else is affected.

---

## Rules of Thumb

- **One environment per project**, in the project folder.
- Always activate it **before** `pip install` (check for the `(.venv)` prefix).
- Never use `sudo pip install`.
- Keep `requirements.txt` up to date, and keep `.venv/` out of Git.
- VS Code and PyCharm can detect a `.venv` folder and use it as the project's interpreter. Select it in the editor.

---

## Common Problems

| Problem | Cause | Fix |
|---|---|---|
| Package installed but `ModuleNotFoundError` | The environment was not active, so pip installed globally | Activate, then install again |
| `python` still shows the global one | Activation failed or you used a new terminal | Activate in that terminal |
| `activate` not found | Wrong folder, or wrong command for your system | Check the table above (`Scripts` on Windows, `bin` elsewhere) |
| `The virtual environment was not created successfully because ensurepip is not available` (Debian / Ubuntu) | `python3-venv` is not installed | `sudo apt install python3-venv` |
| Environment stopped working after moving the folder | Paths inside it are fixed | Recreate it from `requirements.txt` |
| Python version upgraded and the environment broke | The environment points to the old interpreter | Recreate it |

---

## Workflow Summary

```
cd my_project
python -m venv .venv                       # 1. create (once)
source .venv/bin/activate                  # 2. activate (every new terminal)
python -m pip install requests             # 3. install what you need
python -m pip freeze > requirements.txt    # 4. save the list
python app.py                              # 5. work
deactivate                                 # 6. finish
```
(Windows: replace step 2 with `.venv\Scripts\activate`.)

---

- [Back to section index](./README.md)
