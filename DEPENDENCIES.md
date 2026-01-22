# Dependencies

This document describes how to install the system dependencies required to build Paper Mario.

## Table of Contents

- [Common Setup](#common-setup)
- [Platform-Specific Packages](#platform-specific-packages)
  - [WSL (Ubuntu)](#wsl-ubuntu)
  - [macOS](#macos)
  - [Debian/Ubuntu](#debianubuntu)
  - [Fedora](#fedora)
  - [Arch Linux](#arch-linux)
  - [openSUSE](#opensuse)
  - [Alpine Linux](#alpine-linux)
- [Extra Dependencies (for contributors)](#extra-dependencies-for-contributors)

## Common Setup

After installing platform-specific packages, complete these steps for all platforms:

### Python Virtual Environment

Create and activate a virtual environment to isolate Python dependencies:

```sh
python3 -m venv .venv
source .venv/bin/activate  # On Windows (WSL): source .venv/bin/activate
```

> **Note:** You'll need to activate the virtual environment (`. .venv/bin/activate`) each time you open a new terminal session.

### Python Dependencies

Install the required Python packages in your virtual environment:
```sh
python3 -m pip install -U -r tools/configure/requirements.txt
```

### Git Hook

Install the pre-commit hook:
```sh
cp tools/precommit_check_no_assets.sh "$(git rev-parse --git-path hooks)/pre-commit"
```

## Platform-Specific Packages

Choose your platform below and install the required system packages.

## WSL (Ubuntu)

For Windows Subsystem for Linux using Ubuntu, follow the Debian/Ubuntu instructions below. First, ensure your system is up to date:

```sh
sudo apt update && sudo apt upgrade
```

Then proceed with the [Debian/Ubuntu](#debianubuntu) package installation.

## macOS

### Prerequisites

Install [Homebrew](https://brew.sh/) if you don't already have it:
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Required Packages

Install the required packages using Homebrew:
```sh
brew install md5sha1sum ninja gcc bates64/brew/mips-linux-gnu-binutils bates64/brew/mips-linux-gnu-gcc
```

> **Note:** If you get an error about `md5sha1sum` conflicting with `coreutils`:
> ```
> Error: Cannot install md5sha1sum because conflicting formulae are installed.
>   coreutils: because both install `md5sum` and `sha1sum` binaries
>
> Please `brew unlink coreutils` before continuing.
> ```
> You can safely skip installing `md5sha1sum` if you already have `coreutils` installed, as it provides the same functionality.

## Debian/Ubuntu

Install the required packages using apt:
```sh
sudo apt install -y curl git python3 python3-pip python3-venv build-essential binutils-mips-linux-gnu zlib1g-dev libyaml-dev ninja-build cpp-mips-linux-gnu gcc-mips-linux-gnu
```

## Fedora

Install the required packages using dnf:
```sh
sudo dnf install -y curl git python3 python3-pip ninja-build gcc-mips64-linux-gnu libyaml-devel zlib-devel
sudo dnf group install -y "C Development Tools and Libraries" "Development Tools"
```

### MIPS Binutils

Check if MIPS binutils are already installed:
```sh
mips-linux-gnu-ar --version
```

If the command is not found, you'll need to build and install binutils from source:

```sh
# Download binutils
wget https://ftp.gnu.org/gnu/binutils/binutils-2.35.tar.bz2
tar -xf binutils-2.35.tar.bz2
cd binutils-2.35

# Configure
sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure
./configure --target=mips-linux-gnu \
            --with-sysroot=/usr/mips-linux-gnu \
            --prefix=/usr \
            --disable-multilib \
            --with-gnu-as \
            --with-gnu-ld \
            --disable-nls \
            --enable-ld=default \
            --enable-plugins \
            --enable-deterministic-archives \
            --disable-werror

# Build and install
sudo make
sudo make install

# Clean up
cd ..
rm -rf binutils-2.35 binutils-2.35.tar.bz2
```

## Arch Linux

First, update your system packages:
```sh
sudo pacman -Syu
```

Install the required packages using pacman:
```sh
sudo pacman -S --noconfirm --needed curl git python python-pip base-devel zlib libyaml ninja
```

### MIPS Binutils

Check if MIPS binutils are already installed:
```sh
mips-linux-gnu-ar --version
```

If the command is not found, install `mips-linux-gnu-binutils` from the AUR. You can use an AUR helper like `yay`, `aura`, or `yaourt`:

**Using yay:**
```sh
yay -S --noconfirm mips-linux-gnu-binutils
```

**Using aura:**
```sh
sudo aura -A --noconfirm mips-linux-gnu-binutils
```

**Using yaourt:**
```sh
sudo yaourt -S --noconfirm mips-linux-gnu-binutils
```

**Manual installation (if you don't have an AUR helper):**
```sh
git clone https://aur.archlinux.org/mips-linux-gnu-binutils.git
cd mips-linux-gnu-binutils
makepkg -si
cd ..
rm -rf mips-linux-gnu-binutils
```

## openSUSE

Install the required packages using zypper:
```sh
sudo zypper -n install curl git python3 python3-devel python3-pip gcc gcc-c++ glibc-devel make cross-mips-binutils zlib-devel libyaml-devel ninja
```

### Create Symbolic Links

openSUSE uses different naming for MIPS binutils tools. Create symbolic links to the expected names:
```sh
sudo ln -s /usr/bin/mips-suse-linux-addr2line /usr/bin/mips-linux-gnu-addr2line
sudo ln -s /usr/bin/mips-suse-linux-ar /usr/bin/mips-linux-gnu-ar
sudo ln -s /usr/bin/mips-suse-linux-as /usr/bin/mips-linux-gnu-as
sudo ln -s /usr/bin/mips-suse-linux-elfedit /usr/bin/mips-linux-gnu-elfedit
sudo ln -s /usr/bin/mips-suse-linux-gprof /usr/bin/mips-linux-gnu-gprof
sudo ln -s /usr/bin/mips-suse-linux-ld /usr/bin/mips-linux-gnu-ld
sudo ln -s /usr/bin/mips-suse-linux-ld.bfd /usr/bin/mips-linux-gnu-ld.bfd
sudo ln -s /usr/bin/mips-suse-linux-nm /usr/bin/mips-linux-gnu-nm
sudo ln -s /usr/bin/mips-suse-linux-objcopy /usr/bin/mips-linux-gnu-objcopy
sudo ln -s /usr/bin/mips-suse-linux-objdump /usr/bin/mips-linux-gnu-objdump
sudo ln -s /usr/bin/mips-suse-linux-ranlib /usr/bin/mips-linux-gnu-ranlib
sudo ln -s /usr/bin/mips-suse-linux-readelf /usr/bin/mips-linux-gnu-readelf
sudo ln -s /usr/bin/mips-suse-linux-size /usr/bin/mips-linux-gnu-size
sudo ln -s /usr/bin/mips-suse-linux-strings /usr/bin/mips-linux-gnu-strings
sudo ln -s /usr/bin/mips-suse-linux-strip /usr/bin/mips-linux-gnu-strip
```

## Alpine Linux

### Enable Edge Community Repository

If the edge/community repository isn't present in `/etc/apk/repositories`, add it:
```sh
echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" | sudo tee -a /etc/apk/repositories
```

### Required Packages

Install the required packages using apk:
```sh
sudo apk add --no-cache bash curl wget git python3 python3-dev py3-pip build-base zlib-dev yaml-dev ninja
```

### MIPS Binutils

Check if MIPS binutils are already installed:
```sh
mips-linux-gnu-ar --version
```

If the command is not found, you'll need to build and install binutils from source:

```sh
# Download binutils
wget https://ftp.gnu.org/gnu/binutils/binutils-2.35.tar.bz2
tar -xf binutils-2.35.tar.bz2
cd binutils-2.35

# Configure
sed -i "/ac_cpp=/s/\$CPPFLAGS/\$CPPFLAGS -O2/" libiberty/configure
./configure --target=mips-linux-gnu \
            --with-sysroot=/usr/mips-linux-gnu \
            --prefix=/usr \
            --disable-multilib \
            --with-gnu-as \
            --with-gnu-ld \
            --disable-nls \
            --enable-ld=default \
            --enable-plugins \
            --enable-deterministic-archives \
            --disable-werror

# Build and install
sudo make
sudo make install

# Clean up
cd ..
rm -rf binutils-2.35 binutils-2.35.tar.bz2
```

## Extra Dependencies (for contributors)

The following packages are optional but useful for contributors who want to work on code quality, documentation, or use additional development tools.

### System Packages

**macOS:**
```sh
# No additional system packages required
```

**Debian/Ubuntu / WSL:**
```sh
sudo apt install -y clang-tidy astyle doxygen
```

**Fedora:**
```sh
sudo dnf install -y clang-tools-extra astyle doxygen
```

**Arch Linux:**
```sh
sudo pacman -S --noconfirm --needed clang astyle doxygen
```

**openSUSE:**
```sh
sudo zypper -n install clang astyle doxygen
```

**Alpine Linux:**
```sh
sudo apk add --no-cache clang-extra-tools astyle doxygen
```

### Python Packages

After installing system packages, install the extra Python dependencies in your virtual environment:
```sh
python3 -m pip install -U -r tools/configure/requirements_extra.txt
```

## Other Distributions

If your distribution is not listed above, you'll need to install the following packages (names may vary):

- **Build essentials:** gcc, make, binutils
- **MIPS cross-compilation tools:** binutils-mips-linux-gnu, gcc-mips-linux-gnu (or equivalent)
- **Development libraries:** zlib, libyaml
- **Build tools:** ninja
- **Version control:** git
- **Python:** python3, pip, venv (for virtual environments)
- **Utilities:** curl, wget (for downloading files)

Please consider contributing installation instructions for your distribution by opening a pull request!
