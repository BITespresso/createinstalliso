# createinstalliso

Creates a bootable ISO image from a macOS installer application. This image can then be used to install macOS on a physical or virtual machine.

## Table of Contents

1. [Introduction](#user-content-introduction)
2. [Requirements](#user-content-requirements)
3. [Compatibility](#user-content-compatibility)
4. [Installation](#user-content-installation)
5. [Usage](#user-content-usage)
    1. [Example for the macOS Catalina installer](#user-content-example-for-the-macos-catalina-installer)
    2. [Command Line Arguments](#user-content-command-line-arguments)
6. [Troubleshooting](#user-content-troubleshooting)
    1. [Alert: This copy of the Install ... can't ...](#user-content-alert-this-copy-of-the-install--cant-)
    2. [Error message: ... contains a broken createinstallmedia command.](#user-content-error-message--contains-a-broken-createinstallmedia-command)
7. [References](#user-content-references)
    1. [Installer Application Types](#user-content-installer-application-types)
    2. [Required external commands](#user-content-required-external-commands)
    3. [Exit status and messages](#user-content-exit-status-and-messages)
8. [License](#user-content-license)

## Introduction

If you have ever needed to install macOS on a new computer, you have probably discovered the ability to create a bootable USB flash drive or other volume by using the `createinstallmedia` command included in the downloaded macOS installer: [How to create a bootable installer for macOS](https://support.apple.com/en-us/HT201372).

In case you need a **bootable ISO image** instead, you can find a lot of shell scripts in forums that create such an ISO image for certain macOS versions. However, they are often only written for a single macOS version and cannot adapt to the specific macOS version contained in the installation program.

The purpose of this program **createinstalliso** is to create a bootable ISO image from a downloaded macOS installer application. In addition, the program should:

* Automatically adapt the strategy used to create the ISO image to the installer application type, thereby supporting a wide range of macOS versions (see: [Compatibility](#user-content-compatibility)).
* Look and feel like Apple's `createinstallmedia` command (e.g. handling of command line arguments, wording for error and progress messages, status codes used when exiting).
* Behaving like a standard command line program, even though it is a Bash script (e.g. Ctrl-C interruptibility, proper cleanup of resources before exiting).

## Requirements

To run **createinstalliso** you need:

* A machine running Mac OS X Snow Leopard 10.6 or later.
* An administrator account that has a password to execute a `sudo` command.
* A downloaded full size macOS installer application for Mac OS X Lion 10.7 or later (see: [Compatibility](#user-content-compatibility)).

**Note:** Make sure that you have downloaded a *full size* macOS installer application which should have a size of at least 5 GB.

## Compatibility

### macOS version

The table below shows:

* Whether a macOS version can run **createinstalliso**.
* Which installer applications can be used with this particular macOS version.

| macOS                 | Version | Can run **createinstalliso** | Can use installer for |
| --------------------- | ------- |:----------------------------:| --------------------- |
| Mac OS X Cheetah      | 10.0    | No                           | -                     |
| Mac OS X Puma         | 10.1    | No                           | -                     |
| Mac OS X Jaguar       | 10.2    | No                           | -                     |
| Mac OS X Panther      | 10.3    | No                           | -                     |
| Mac OS X Tiger        | 10.4    | No                           | -                     |
| Mac OS X Leopard      | 10.5    | No                           | -                     |
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

* Which macOS version is required to use **createinstalliso** with this particular installer.

| Installer for         | Version | Required macOS version |
| --------------------- | ------- |:----------------------:|
| Mac OS X Cheetah      | 10.0    | -                      |
| Mac OS X Puma         | 10.1    | -                      |
| Mac OS X Jaguar       | 10.2    | -                      |
| Mac OS X Panther      | 10.3    | -                      |
| Mac OS X Tiger        | 10.4    | -                      |
| Mac OS X Leopard      | 10.5    | -                      |
| Mac OS X Snow Leopard | 10.6    | -                      |
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

* Open Terminal, which is in the Utilities folder of your Applications folder and type or paste the following commands in Terminal.
* Download the **createinstalliso** file.
    ```
    curl -fOSs https://raw.githubusercontent.com/BITespresso/createinstalliso/master/createinstalliso
    ```
* Make the downloaded script executable.
    ```
    chmod +x createinstalliso
    ```
* A good practice is to keep user shell scripts in the `bin` folder in your home directory. If it does not already exist, create it and move the script into this folder.
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

### Example for the macOS Catalina installer

* Open Terminal, which is in the Utilities folder of your Applications folder.
* Type or paste the following command in Terminal. This assumes that the installer is still in your Applications folder, and you have moved **createinstalliso** to the `~/bin` folder.
    ```
    sudo ~/bin/createinstalliso --isodirectory ~/Desktop/ --applicationpath /Applications/Install\ macOS\ Catalina.app/
    ```
* Press Return after typing the command.
* When prompted, type your administrator password and press Return again. Terminal doesn't show any characters as you type your password.
* Terminal shows the progress as the bootable installer is created.
* When Terminal says that it's done, you will find the bootable ISO image file `Install macOS Catalina.iso` in the folder you specified (here: `~/Desktop`).
* You can now quit Terminal.

### Command Line Arguments

The command **createinstalliso** requires the following two command line arguments:

* `--isodirectory` (or `-i`) must be followed by a path to a directory where the installer ISO image file will be created.
* `--applicationpath` (or `-a`) must be followed by the path to the OS installer application that should be used to create the bootable ISO image.

The name of the ISO image to be created is made up of the name of the installer application and has the extension `iso` (e.g. `Install macOS Catalina.iso`). If such a file already exists in the specified destination directory, the user will be prompted for confirmation before this file is overwritten. If you do not care about overwriting an existing file and do not want to be prompted, you can add the command line option:

* `--nointeraction` (or `-n`) which causes an existing ISO image file to **always** be overwritten.

## Troubleshooting

### Alert: This copy of the Install ... can't ...

If you try to install macOS using the ISO image you created, you may receive an error dialog with a message like ***"This copy of the Install OS X El Capitan application can't be verified. It may have been corrupted or tampered with during downloading."*** or ***"This copy of the Install macOS Mojave application is damaged, and can't be used to install macOS."***

The reason for these rather misleading error messages is that the certificates included in many macOS installer applications expired on October 24, 2019, leading to the error: [If an installer says it can't be verified or was signed with a certificate that has expired](https://support.apple.com/en-us/HT208052)

A *simple* workaround is to download the updated installers with *new* certificates that Apple provides on this page: [If an installer says it can't be verified or was signed with a certificate that has expired](https://support.apple.com/en-us/HT208052)

But unfortunately there is a bug in the **updated** installer for **macOS Sierra** which makes this particular installer unusable (see: "[Error message: ... is not a valid volume mount point.](#user-content-error-message--is-not-a-valid-volume-mount-point)"). In this case, you must manually set the system date to a date in the past when the certificate was still valid:

* Close the error dialog.
* Open "Terminal" from the "Utilities" menu of the macOS Utilities.
* In the terminal window type `date 071900002017`. This sets the system date to July 19, 2017, 0:00 am, the release date of macOS Sierra.
* Quit the terminal window and resume the installation of macOS Sierra.

### Error message: ... contains a broken createinstallmedia command.

If **createinstalliso** terminates with the error message `... contains a broken createinstallmedia command.` you are using the *newer* **macOS Sierra** installer like the one in: [How to upgrade to macOS Sierra](https://support.apple.com/en-us/HT208202)

While this *newer* installer application for macOS Sierra contains updated certificates (see: "[Alert: This copy of the Install ... can't ...](#user-content-alert-this-copy-of-the-install--cant-)"), it unfortunately introduced a bug that makes the included `createinstallmedia` command useless because it **always** complains about the mount point. Since `createinstallmedia` is used internally by **createinstalliso**, it is not possible to use this *newer* version of the macOS Sierra installer application.

Therefore, the only solution is to obtain an *older* macOS Sierra installer application and follow the procedure described in the section "[Alert: This copy of the Install ... can't ...](#user-content-alert-this-copy-of-the-install--cant-)" to work around the problem with the expired certificates in the *older* installer application.

## References

### Installer Application Types

Apple has used different internal structures for its macOS installers over time. Therefore, **createinstalliso** examines the internal structure of the installer application and determines which 'type' it is. Based on this 'type', the necessary steps to create a bootable ISO image are then performed.

The known installer application types are:

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

**createinstalliso** uses a number of external commands, which must be available on your system: `awk`, `bless`, `cp`, `df`, `du`, `hdiutil`, `mktemp`, `ps`, `pwd`, `rm`, `script`, `seq`, `stat`, `sw_vers`, `tput`, `uname` and `/usr/libexec/PlistBuddy`.

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
| 222    | \[FILE\] contains a broken createinstallmedia command.                                |
| 221    | Couldn't get installer application minimum macOS version.                             |

## License

Copyright (C) 2021 Michael Berger (BITespresso)

**createinstalliso** is licensed under the **GNU General Public License v3 (GPL-3)** (http://www.gnu.org/copyleft/gpl.html).
