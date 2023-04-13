# Disclaimer

This project is in no way related to or created by the official Fedora team or its members. It is solely a project of mine.

# FedoraWSL2
Fedora rawhide on WSL2 (Windows 10 FCU or later) based on [wsldl](https://github.com/yuk7/wsldl).

[![Screenshot-2023-04-11-114235.png](https://i.postimg.cc/YCs4DwY4/Screenshot-2023-04-11-114235.png)](https://postimg.cc/5X8NjTP1)
[![Github All Releases](https://img.shields.io/github/downloads/sileshn/FedoraWSL2/total.svg?style=flat-square)](https://github.com/sileshn/FedoraWSL2/releases)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
[![License](https://img.shields.io/github/license/sileshn/FedoraWSL2.svg?style=flat-square)](https://raw.githubusercontent.com/sileshn/FedoraWSL2/master/LICENSE?token=GHSAT0AAAAAACA2QMWW7BDABA5NM5RE3ADYZBXNQ5A)

## Features and important information
FedoraWSL2 has the following features during the installation stage.
* Increase virtual disk size from the default 256GB
* Create a new user and set the user as default
* FedoraWSL2 Supports systemd natively if you are running wsl v0.67.6 (more details [here](https://devblogs.microsoft.com/commandline/systemd-support-is-now-available-in-wsl/)) and above. For earlier versions of wsl, systemd is supported using diddledani's [one-script-wsl2-systemd](https://github.com/diddledani/one-script-wsl2-systemd). This is done automatically during initial setup.
* FedoraWSL2 includes a wsl.conf file which only has section headers. Users can use this to configure the distro to their liking. You can read more about wsl.conf and its configuration settings [here](https://docs.microsoft.com/en-us/windows/wsl/wsl-config).

## Requirements
* For x64 systems: Version 1903 or higher, with Build 18362 or higher.
* For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
* Builds lower than 18362 do not support WSL 2.
* If you are running Windows 10 version 2004 or higher, you can install all components required to run wsl2 with a single command. This will install ubuntu by default. More details are available [here](https://devblogs.microsoft.com/commandline/install-wsl-with-a-single-command-now-available-in-windows-10-version-2004-and-higher/).
	```cmd
	wsl.exe --install
	```
* If you are running Windows 10 lower then version 2004, follow the steps below to install requirements manually. For more details, check [this](https://docs.microsoft.com/en-us/windows/wsl/install-manual) microsoft document.
	* Enable Windows Subsystem for Linux feature.
	```cmd
	dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
	```
	* Enable Virtual Machine feature
	```cmd
	dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
	```
	* Download and install the latest Linux kernel update package from [here](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi).

## Install
* Make sure all the steps mentioned under "Requirements" are completed.
* [Download](https://github.com/sileshn/FedoraWSL2/releases/latest) installer zip
* Extract all files in zip file to same directory
* Set version 2 as default. Note that this step is required only for manual installation.
  ```dos
  wsl --set-default-version 2
  ```
* Run Fedora.exe to extract rootfs and register to WSL

**Note:**
Exe filename is using the instance name to register. If you rename it you can register with a diffrent name and have multiple installs.

## How-to-Use(for Installed Instance)
#### exe Usage
```
Usage :
    <no args>
      - Open a new shell with your default settings.

    run <command line>
      - Run the given command line in that instance. Inherit current directory.

    runp <command line (includes windows path)>
      - Run the given command line in that instance after converting its path.

    config [setting [value]]
      - `--default-user <user>`: Set the default user of this instance to <user>.
      - `--default-uid <uid>`: Set the default user uid of this instance to <uid>.
      - `--append-path <true|false>`: Switch of Append Windows PATH to $PATH
      - `--mount-drive <true|false>`: Switch of Mount drives
      - `--default-term <default|wt|flute>`: Set default type of terminal window.

    get [setting]
      - `--default-uid`: Get the default user uid in this instance.
      - `--append-path`: Get true/false status of Append Windows PATH to $PATH.
      - `--mount-drive`: Get true/false status of Mount drives.
      - `--wsl-version`: Get the version os the WSL (1/2) of this instance.
      - `--default-term`: Get Default Terminal type of this instance launcher.
      - `--lxguid`: Get WSL GUID key for this instance.

    backup [contents]
      - `--tar`: Output backup.tar to the current directory.
      - `--reg`: Output settings registry file to the current directory.
	  - `--tgz`: Output backup.tar.gz to the current directory.
      - `--vhdx`: Output backup.ext4.vhdx to the current directory.
      - `--vhdxgz`: Output backup.ext4.vhdx.gz to the current directory.

    clean
      - Uninstall that instance.

    help
      - Print this usage message.
```

#### Just Run exe
```cmd
>{InstanceName}.exe
[root@PC-NAME user]#
```

#### Run with command line
```cmd
>{InstanceName}.exe run uname -r
4.4.0-43-Microsoft
```

#### Run with command line with path translation
```cmd
>{InstanceName}.exe runp echo C:\Windows\System32\cmd.exe
/mnt/c/Windows/System32/cmd.exe
```

#### Change Default User(id command required)
```cmd
>{InstanceName}.exe config --default-user user

>{InstanceName}.exe
[user@PC-NAME dir]$
```

#### Set "Windows Terminal" as default terminal
```cmd
>{InstanceName}.exe config --default-term wt
```

## How to setup

FedoraWSL2 will ask you to create a new user during its first run. If you chose to create a new user during initial setup, the steps below are not required unless you want to create additional users.
```dos
passwd
useradd -m -g users -G wheel -s /bin/bash <username>
echo "%sudo ALL=(ALL) ALL" >/etc/sudoers.d/wheel
passwd <username>
exit
```

You can set the user you created as default user using 2 methods.

Open Fedora.exe, run the following command (replace username with the actual username you created).
```dos
sed -i '/\[user\]/a default = username' /etc/wsl.conf
```

Shutdown and restart the distro (this step is important).

(or)

Execute the command below in a windows cmd terminal from the directory where Fedora.exe is installed.
```dos
>Fedora.exe config --default-user <username>
```

## How to uninstall instance
```dos
>Fedora.exe clean

```

## How to backup instance
export to backup.tar.gz
```cmd
>Fedora.exe backup --tgz
```
export to backup.ext4.vhdx.gz
```cmd
>Fedora.exe backup --vhdxgz
```

## How to restore instance

There are 2 ways to do it. 

Rename the backup to rootfs.tar.gz and run Fedora.exe

(or)

.tar(.gz)
```cmd
>Fedora.exe install backup.tar.gz
```
.ext4.vhdx(.gz)
```cmd
>Fedora.exe install backup.ext4.vhdx.gz
```

You may need to run the command below in some circumstances.
```cmd
>Fedora.exe --default-uid 1000
```

## How to build

#### Prerequisites

Docker, tar, zip, unzip, bsdtar need to be installed.

```dos
git clone git@gitlab.com:sileshn/FedoraWSL2.git
cd FedoraWSL2
make

```
Copy the FedoraRawhide.zip file to a safe location and run the command below to clean.
```dos
make clean

```

