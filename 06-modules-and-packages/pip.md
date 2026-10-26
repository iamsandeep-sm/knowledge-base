# pip - Revision Notes ~ sm

> **In one line:** **pip** is Python's package installer. It downloads third-party packages from **PyPI** (the Python Package Index, at pypi.org) and installs them so you can `import` them. Always run it as **`python -m pip`**, ideally inside a virtual environment.

## Table of Contents
- [What is pip?](#what-is-pip)
- [Check pip](#check-pip)
- [Install a Package](#install-a-package)
- [Choosing a Version](#choosing-a-version)
- [Upgrade and Uninstall](#upgrade-and-uninstall)
- [See What is Installed](#see-what-is-installed)
- [requirements.txt](#requirementstxt)
- [Why python -m pip?](#why-python--m-pip)
- [The externally-managed-environment Error](#the-externally-managed-environment-error)
- [Using an Installed Package](#using-an-installed-package)
- [Command Cheat Table](#command-cheat-table)
- [Common Problems](#common-problems)
- [Safety Tips](#safety-tips)

---

## What is pip?

The standard library covers many things, but the community has written thousands more packages (`requests`, `numpy`, `pandas`, `flask` ...). pip finds them on PyPI, downloads them, and also installs **the packages they depend on**.

| Term | Meaning |
|---|---|
| **PyPI** | The online store of Python packages |
| **pip** | The tool that installs from it |
| **Package / distribution** | A downloadable bundle of code with a name and a version |
| **Dependency** | A package that another package needs |

---

## Check pip

pip is included with Python.

```
python -m pip --version
```
```
pip 26.2.2 from .../site-packages/pip (python 3.13)
```
(Your version will differ.) On macOS and Linux write `python3` instead of `python`. The text in brackets shows **which Python** this pip belongs to, which is useful when you have several.

---

## Install a Package

```
python -m pip install six
```
pip downloads the package, installs it, and prints a "Successfully installed ..." line.

Several at once:
```
python -m pip install requests six
```

---

## Choosing a Version

| Command | Meaning |
|---|---|
| `pip install six` | Latest version |
| `pip install six==1.16.0` | Exactly this version |
| `pip install "six>=1.15"` | This version or newer |
| `pip install "six>=1.15,<2"` | Newer than 1.15 but below 2 |
| `pip install six~=1.16` | Compatible release: `>=1.16, ==1.*` |

Put the version expression in **quotes** when it contains `>` or `<`, otherwise the terminal reads them as redirection symbols.

If the version does not exist:
```
ERROR: Could not find a version that satisfies the requirement six==99
ERROR: No matching distribution found for six==99
```

---

## Upgrade and Uninstall

```
python -m pip install --upgrade six
python -m pip uninstall six
```
`uninstall` asks for confirmation; add `-y` to skip it. To upgrade pip itself:
```
python -m pip install --upgrade pip
```

---

## See What is Installed

| Command | Shows |
|---|---|
| `pip list` | Every installed package and its version |
| `pip show six` | Details of one package (version, summary, location, what it requires) |
| `pip freeze` | Packages in `name==version` form, ready for a requirements file |
| `pip list --outdated` | Packages that have a newer version |

```
python -m pip list
```
```
Package Version
------- -------
pip     26.2.2
six     1.16.0
```
```
python -m pip freeze
```
```
six==1.16.0
```

---

## requirements.txt

A **requirements file** records the packages a project needs, so anyone can install the same set.

**Create it from the current environment:**
```
python -m pip freeze > requirements.txt
```
**Install everything from it** (on another computer or a fresh environment):
```
python -m pip install -r requirements.txt
```
A requirements file is a plain text file, one package per line:
```
six==1.16.0
requests>=2.31
```
Lines that start with `#` are comments. For a clean list, freeze **inside the project's virtual environment** so that only the project's packages appear.

---

## Why python -m pip?

Many computers have more than one Python. The plain command `pip` may belong to a **different** Python than the `python` you run, so the package is installed in one place and imported from another, which gives `ModuleNotFoundError`.

`python -m pip ...` always uses the pip of **that** `python`, so they match.

```
python -m pip install six       # installs for this python
```

---

## The externally-managed-environment Error

Some systems (many Linux distributions, and some Homebrew setups) mark the system Python as **externally managed**. Installing there with pip is blocked on purpose, to protect tools that depend on the system packages:

```
error: externally-managed-environment
```
**The right fix: use a virtual environment** (see the next note):
```
python3 -m venv .venv
source .venv/bin/activate
python -m pip install six
```
Avoid forcing it with `--break-system-packages`, which can damage the operating system's own Python tools.

---

## Using an Installed Package

After installing, import it like any module:

```python
import six

print(six.PY3)
# Output: True
```
(This works only after `python -m pip install six` has been done for the same Python.)

---

## Command Cheat Table

| Task | Command |
|---|---|
| Install | `python -m pip install name` |
| Install a version | `python -m pip install name==1.2.3` |
| Upgrade | `python -m pip install -U name` |
| Uninstall | `python -m pip uninstall name` |
| List | `python -m pip list` |
| Details | `python -m pip show name` |
| Save list | `python -m pip freeze > requirements.txt` |
| Install from list | `python -m pip install -r requirements.txt` |
| Help | `python -m pip help` |

---

## Common Problems

| Problem | Likely cause | Fix |
|---|---|---|
| `'pip' is not recognized` / `command not found` | pip is not on `PATH` | Use `python -m pip` |
| Package installed but `ModuleNotFoundError` | Installed for a different Python | `python -m pip install ...` with the same `python` |
| `Permission denied` | Installing into the system Python | Use a virtual environment (do not use `sudo pip`) |
| `externally-managed-environment` | System Python is protected | Use a virtual environment |
| `No matching distribution found` | Wrong name or version, or no internet, or the package does not support your Python version | Check the name on pypi.org |
| Warning about running pip as `root` | Installing as the administrator user | Use a virtual environment |

---

## Safety Tips

- Install only packages you trust. Check the name carefully: names that look almost the same as popular packages exist on PyPI to trick people (typosquatting).
- Prefer packages that are well known and still maintained.
- Pin versions in `requirements.txt` for projects you want to repeat exactly.
- Never install packages with administrator rights unless you must.

---

- [Back to section index](./README.md)
