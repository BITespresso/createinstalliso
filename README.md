# createinstalliso

Creates a bootable ISO image from a macOS installer application. This image can then be used to install macOS on a physical or virtual machine.

## Table of Contents

1. [Introduction](#user-content-introduction)
2. [Requirements](#user-content-requirements)
3. [Compatibility](#user-content-compatibility)
4. [Installation](#user-content-installation)
5. [Usage](#user-content-usage)
    1. [Command line arguments](#user-content-command-line-arguments)
    2. [Example](#user-content-example)
6. [Troubleshooting](#user-content-troubleshooting)
    1. [Alert during macOS installation](#user-content-alert-during-macos-installation)
    2. [Install macOS Sierra (Version 12.6.06)](#user-content-install-macos-sierra--version-12-6-06-)
7. [References](#user-content-references)
    1. [Installer application types](#user-content-installer-application-types)
    2. [Required external commands](#user-content-required-external-commands)
    3. [Exit status and messages](#user-content-exit-status-and-messages)
8. [License](#user-content-license)

## Introduction

If you have ever needed to install macOS on a new computer, you have probably discovered the ability to create a bootable USB flash drive or other volume by using the `createinstallmedia` command included in the downloaded macOS installer: [Create a bootable installer](https://support.apple.com/en-us/101578).

In case you need a **bootable ISO image** instead, you can find a lot of shell scripts in forums that create such an ISO image for certain macOS versions. However, they are often only written for a single macOS version and cannot adapt to the specific macOS version contained in the installer.

The purpose of this program **createinstalliso** is to create a bootable ISO image from a downloaded full macOS installer. In addition, the program should:

* Automatically adapt the strategy used to create the ISO image to the installer application type, thereby supporting a wide range of macOS versions (see: [Compatibility](#user-content-compatibility)).
* Look and feel like Apple's `createinstallmedia` command (e.g. handling of command line arguments, wording for error and progress messages, status codes used when exiting).
* Behaving like a standard command line program (e.g. Ctrl-C interruptibility, proper cleanup of resources before exiting), even though it is a Bash script.

## Requirements

To run **createinstalliso** you need:

* A machine running Mac OS X Snow Leopard 10.6 or later.
* An administrator user and password required for the use of the `sudo` command.
* A downloaded full macOS installer for Mac OS X Lion 10.7 or later.

**Note:** Make sure that you have downloaded a *full* macOS installer which should have a size of at least 5 GB.

## Compatibility

### macOS version

The table below shows:

* Whether a macOS version can run **createinstalliso**.
* Which macOS installers can be used to create an ISO image with this particular macOS version.

| macOS                 | Version | Can run **createinstalliso** | Can use installer for |
| --------------------- | ------- |:----------------------------:| --------------------- |
| Mac OS X Cheetah      | 10.0    | No                           | N/A                   |
| Mac OS X Puma         | 10.1    | No                           | N/A                   |
| Mac OS X Jaguar       | 10.2    | No                           | N/A                   |
| Mac OS X Panther      | 10.3    | No                           | N/A                   |
| Mac OS X Tiger        | 10.4    | No                           | N/A                   |
| Mac OS X Leopard      | 10.5    | No                           | N/A                   |
| Mac OS X Snow Leopard | 10.6    | Yes                          | 10.7 - 10.11          |
| Mac OS X Lion         | 10.7    | Yes                          | 10.7 - 10.11          |
| OS X Mountain Lion    | 10.8    | Yes                          | 10.7 - 10.15          |
| OS X Mavericks        | 10.9    | Yes                          | 10.7 - 12             |
| OS X Yosemite         | 10.10   | Yes                          | 10.7 - 12             |
| OS X El Capitan       | 10.11   | Yes                          | 10.7 - 13             |
| macOS Sierra          | 10.12   | Yes                          | 10.7 - 13             |
| macOS High Sierra     | 10.13   | Yes                          | 10.7 - 14             |
| macOS Mojave          | 10.14   | Yes                          | 10.7 - 14             |
| macOS Catalina        | 10.15   | Yes                          | 10.7 - 14             |
| macOS Big Sur         | 11      | Yes                          | 10.7 - 14             |
| macOS Monterey        | 12      | Yes                          | 10.7 - 14             |
| macOS Ventura         | 13      | Yes                          | 10.7 - 14             |
| macOS Sonoma          | 14      | Yes                          | 10.7 - 14             |

### Installer version

The table below shows:

* Which macOS version is required to use **createinstalliso** with a particular macOS installer.

| Installer for         | Version | Required macOS version |
| --------------------- | ------- |:----------------------:|
| Mac OS X Cheetah      | 10.0    | N/A                    |
| Mac OS X Puma         | 10.1    | N/A                    |
| Mac OS X Jaguar       | 10.2    | N/A                    |
| Mac OS X Panther      | 10.3    | N/A                    |
| Mac OS X Tiger        | 10.4    | N/A                    |
| Mac OS X Leopard      | 10.5    | N/A                    |
| Mac OS X Snow Leopard | 10.6    | N/A                    |
| Mac OS X Lion         | 10.7    | 10.6 or later          |
| OS X Mountain Lion    | 10.8    | 10.6 or later          |
| OS X Mavericks        | 10.9    | 10.6 or later          |
| OS X Yosemite         | 10.10   | 10.6 or later          |
| OS X El Capitan       | 10.11   | 10.6 or later          |
| macOS Sierra          | 10.12   | 10.8 or later          |
| macOS High Sierra     | 10.13   | 10.8 or later          |
| macOS Mojave          | 10.14   | 10.8 or later          |
| macOS Catalina        | 10.15   | 10.8 or later          |
| macOS Big Sur         | 11      | 10.9 or later          |
| macOS Monterey        | 12      | 10.9 or later          |
| macOS Ventura         | 13      | 10.11 or later         |
| macOS Sonoma          | 14      | 10.13 or later         |

## Installation

Open Terminal, which is in the Utilities folder of your Applications folder, and type or paste the following commands into Terminal:

* Download the **createinstalliso** file.

    ```
    curl -fOSs https://raw.githubusercontent.com/BITespresso/createinstalliso/master/createinstalliso
    ```

* Make the downloaded script executable.

    ```
    chmod +x createinstalliso
    ```

* A good practice is to keep your shell scripts in the `bin` folder in your home directory. If it does not already exist, create it and move the script to this folder.

    ```
    mkdir -p ~/bin
    mv createinstalliso ~/bin/
    ```

* You might also want to hide the `bin` folder from the Finder.

    ```
    chflags hidden ~/bin
    ```

**Note:** Of course you can also choose a different folder such as `/usr/local/bin` to save the script.

## Usage

Just like Apple's `createinstallmedia` command **createinstalliso** must be run as root user. Depending on where you saved the script, the command will look like:

```
sudo ~/bin/createinstalliso --isodirectory <path to ISO directory> --applicationpath <path to OS installer application> [--nointeraction]
```

### Command line arguments

The command **createinstalliso** requires the following two command line arguments:

* `--isodirectory` (or `-i`) must be followed by a path to a directory where the installer ISO image file will be created.
* `--applicationpath` (or `-a`) must be followed by a path to a copy of the OS installer application to create the bootable ISO image from.

The name of the ISO image to be created is made up of the name of the installer and has the extension `iso` (e.g. `Install macOS Catalina.iso`). If such a file already exists in the specified destination directory, you will be prompted for confirmation before this file is overwritten. If you do not care about overwriting an existing file and do not want to be prompted, you can add the command line option `--nointeraction`.

If you want to suppress user interactions when running the command and always allow the required actions, you can add the option:

* `--nointeraction` (or `-n`) which causes an existing ISO image file to be overwritten and/or patch a defective macOS Sierra installer application without prompting for confirmation.

### Example

The following example show the steps required to create an ISO image from a macOS Catalina installer:

* Open Terminal, which is in the Utilities folder of your Applications folder.
* Type or paste the following command into Terminal, then press Return to enter the command. This assumes that the installer is still in your Applications folder, and you have moved **createinstalliso** to the `~/bin` folder.

    ```
    sudo ~/bin/createinstalliso --isodirectory ~/Desktop/ --applicationpath /Applications/Install\ macOS\ Catalina.app/
    ```

* When prompted, type your administrator password. Terminal doesn't show any characters as you type. Then press Return.
* Terminal shows the progress as the bootable ISO image is created.
* When Terminal says that it's done, you will find the bootable ISO image `Install macOS Catalina.iso` in the folder you specified (here: `~/Desktop`).
* You can now quit Terminal.

## Troubleshooting

### Alert during macOS installation

If you try to install macOS using the ISO image you created, you may see an alert that says the application can't be verified and may have been corrupted or tampered with during downloading. Or it says that the package was signed with a certificate that has expired and may not be authentic (see: [If an installer says it can't be verified or was signed with a certificate that has expired](https://support.apple.com/en-us/100622)).

The most likely reason for this message is that the installer was signed with a certificate that expired on October 24, 2019. You must therefore either get a *new* installer signed with a currently valid certificate or temporarily set the system date to a date in the past when the certificate was still valid.

To manually set the system date to a value in the past, perform the following steps after the above warning is displayed during the macOS installation:

* Close the alert.
* Open Terminal from the Utilities menu.
* Type `date 100800002019` into Terminal, then press Return. This sets the system date to October 8, 2019, 0:00 am.
* Quit Terminal and resume the macOS installation.

### Install macOS Sierra (Version 12.6.06)

When Apple released this particular macOS Sierra installer application, a bug was introduced that causes the included `createinstallmedia` command to fail with an error message about an invalid mount point. Thanks to [Eric Knibbe](https://github.com/EricFromCanada) this bug can easily be fixed by changing the value for `CFBundleShortVersionString` from `12.6.06` to `12.6.03` in the file `Info.plist` (see: [#4](https://github.com/BITespresso/createinstalliso/issues/4)).

If **createinstalliso** detects this defective version of the macOS Sierra installer application, it will offer you to temporarily patch the `Info.plist` file by applying the above change. The original `Info.plist` will be automatically restored when **createinstalliso** terminates. This requires, of course, that the installer is on a writable medium.

## References

### Installer application types

Apple has used different internal structures for its macOS installers over time. Therefore, **createinstalliso** examines the internal structure of the installer and determines which 'type' it is. Based on this 'type', the necessary steps to create a bootable ISO image are performed.

The installer application types are:

| Name                  | Version | Type |
| --------------------- | ------- |:----:|
| Mac OS X Lion         | 10.7    | 1    |
| OS X Mountain Lion    | 10.8    | 1    |
| OS X Mavericks        | 10.9    | 2    |
| OS X Yosemite         | 10.10   | 2    |
| OS X El Capitan       | 10.11   | 2    |
| macOS Sierra          | 10.12   | 3    |
| macOS High Sierra     | 10.13   | 3    |
| macOS Mojave          | 10.14   | 3    |
| macOS Catalina        | 10.15   | 3    |
| macOS Big Sur         | 11      | 4    |
| macOS Monterey        | 12      | 4    |
| macOS Ventura         | 13      | 4    |
| macOS Sonoma          | 14      | 4    |

### Required external commands

**createinstalliso** uses a number of external commands, which must be available on your system: `awk`, `bless`, `cp`, `cut`, `df`, `du`, `hdiutil`, `mktemp`, `ps`, `pwd`, `rm`, `script`, `seq`, `stat`, `sw_vers`, `tput`, `uname` and `/usr/libexec/PlistBuddy`.

Unless you have deliberately modified your system, all of the above commands are available on the macOS versions listed in the section "[Compatibility](#user-content-compatibility)".

### Exit status and messages

A failure of **createinstalliso** is indicated by a non-zero exit status. Wherever possible, **createinstalliso** uses an exit status and and exit message identical to createinstallmedia.

The table below lists all possible exit status and corresponding messages:

| Status | Message                                                                               |
| ------ | ------------------------------------------------------------------------------------- |
| 255    | You must specify both the ISO directory and install application path.                 |
| 255    | createinstalliso: unrecognized option \`\[OPTION\]'                                   |
| 255    | createinstalliso: invalid option -- \[OPTION\]                                        |
| 255    | createinstalliso: option \`\[OPTION\]' requires an argument                           |
| 255    | createinstalliso: option requires an argument -- \[OPTION\]                           |
| 255    | createinstalliso: option \`\[OPTION\]' doesn't allow an argument                      |
| 255    | createinstalliso: option \`\[OPTION\]' is ambiguous                                   |
| 254    | \[DIRECTORY\] is not a valid ISO directory.                                           |
| 253    | \[FILE\] does not appear to be a valid OS installer application.                      |
| 252    | This tool must be run in Bash shell.                                                  |
| 251    | Couldn't get operating system name.                                                   |
| 250    | This tool must be run on macOS.                                                       |
| 249    | This tool must be run as root.                                                        |
| 248    | Couldn't get macOS version.                                                           |
| 247    | Invalid macOS version.                                                                |
| 246    | \[VOLUME\] is not large enough for \[FILE(S)\]. An additional \[SIZE\] is needed.     |
| 245    | This tool requires \[VERSION\] or later.                                              |
| 244    | Couldn't get absolute path for \[FILE\].                                              |
| 243    | Couldn't get absolute path for \[DIRECTORY\].                                         |
| 242    | Couldn't get installer application display name.                                      |
| 241    | Couldn't get installer application type.                                              |
| 240    | Failed to create temporary directory.                                                 |
| 239    | Couldn't get device ID.                                                               |
| 238    | You need \[VERSION\] or later to create an ISO image from this installer application. |
| 237    | Failed to delete the \[FILETYPE\] \[FILE\].                                           |
| 236    | Mount of outer dmg failed.                                                            |
| 235    | BaseSystem missing from the outer dmg, damaged installer image?                       |
| 234    | Failed to convert BaseSystem.dmg.                                                     |
| 233    | Failed to resize disk image.                                                          |
| 232    | The disk image did not mount.                                                         |
| 231    | Failed to delete Packages symlink.                                                    |
| 230    | Failed to copy Packages directory.                                                    |
| 229    | The bless of the installer disk image failed.                                         |
| 228    | Failed to copy BaseSystem.dmg.                                                        |
| 227    | Failed to copy BaseSystem.chunklist.                                                  |
| 226    | Couldn't unmount disk image.                                                          |
| 225    | Failed to create disk image.                                                          |
| 224    | Failed to convert disk image into bootable install media.                             |
| 223    | Failed to create ISO image.                                                           |
| 222    | \[NOT USED SINCE 1.1\]                                                                |
| 221    | Couldn't get installer application minimum macOS version.                             |
| 220    | Failed to patch the file \[FILE\], is write-protected?                                |

## License

Copyright (C) 2021-2024 Michael Berger (BITespresso)

**createinstalliso** is licensed under the **GNU General Public License v3 (GPL-3)** (http://www.gnu.org/copyleft/gpl.html).
