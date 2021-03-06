# Quaternion

[![license](https://img.shields.io/github/license/QMatrixClient/quaternion.svg)](https://github.com/QMatrixClient/quaternion/blob/master/COPYING)
![status](https://img.shields.io/badge/status-beta-yellow.svg)
[![release](https://img.shields.io/github/release/QMatrixClient/quaternion/all.svg)](https://github.com/QMatrixClient/Quaternion/releases/latest)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)

Quaternion is a cross-platform desktop IM client for the [Matrix](https://matrix.org) protocol.

## Contacts
Most of talking around Quaternion happens in our Matrix room: [#quaternion:matrix.org](https://matrix.to/#/#quaternion:matrix.org).

You can also file issues at [the project's issue tracker](https://github.com/QMatrixClient/Quaternion/issues). If you have what looks like a security issue, please see respective instructions in CONTRIBUTING.md.

## Download
The latest release (with links to cross-platform source code archives, as well as archived binaries for Windows) can be found at [GitHub Releases page](https://github.com/QMatrixClient/Quaternion/releases/latest). Build instructions for the source code can be found further in this file (see "Building").

Automatic builds for Windows are packaged by AppVeyor CI upon every commit. To get an archive, surf to the [AppVeyor CI page for Quaternion](https://ci.appveyor.com/project/QMatrixClient/quaternion), then go to "Jobs", click on a job for your architecture and find the archive in "Artifacts".

Unofficial package for Arch Linux: https://aur.archlinux.org/packages/quaternion/

Work in progress to get Quaternion to Flathub: https://github.com/flathub/flathub/pull/90.

## Running
Just start the executable in your most preferred way - either from build_dir and from the installed location.

### Before you run it on Windows
Since we can't rely on package management on Windows, Qt libraries and a C++ runtime are packaged/installed together with Quaternion. However, OpenSSL libraries (ssleay32.dll and libeay32.dll) are not installed automatically because of export restrictions. Unless you already have them around (e.g., they are a part of any Qt development installation, see `Tools/<MinGW toolchain>/opt/bin`), your best bet is to find and download these libraries yourself, and either install them system-wide (which probably makes sense as soon as you keep them up-to-date; make sure the location is in your PATH) or put them next to quaternion.exe.

### Configuration and cache files
Quaternion stores its configuration in a way standard for Qt applications. It will read and write the configuration in the user-specific location (creating it if non-existent) and will only read the system-wide location with reasonable defaults if the configuration is nowhere to be found.
- Linux:
  - system-wide: `$XDG_CONFIG_DIR/QMatrixClient/quaternion` or `/etc/xdg/QMatrixClient/quaternion`
  - user-specific: `$HOME/.config/QMatrixClient/quaternion`
- OSX:
  - system-wide: `/Library/Preferences/com.QMatrixClient.quaternion.plist`
  - user-specific: `$HOME/Library/Preferences/com.QMatrixClient.quaternion.plist`
- Windows: registry keys under
  - system-wide: `HKEY_LOCAL_MACHINE\Software\QMatrixClient\quaternion`
  - user-specific: `HKEY_CURRENT_USER\Software\QMatrixClient\quaternion`

NOTE: as of version 0.0.4, your access tokens are stored in the configuration as well; this means that unless your HKEY_USERS registry nodes (Windows)/$HOME files (Linux, OSX) are inaccessible to other users on the same computer, another user can take your access token and impersonate you on Matrix. This is [a known issue](https://github.com/QMatrixClient/Quaternion/issues/181). A workaround is to not set "Stay logged in" flag upon login; in that case access tokens are only stored in memory but you have to login at each Quaternion startup.

Quaternion caches the rooms state on the file system in a conventional location for your platform, as follows:
- Linux: `$HOME/.cache/QMatrixClient/quaternion`
- OSX: `$HOME/Library/Cache/QMatrixClient/quaternion`
- Windows: `%LOCALAPPDATA%/QMatrixClient/quaternion`

Cache files are safe to delete at any time. If Quaternion doesn't find them when starting up it downloads the whole state from Matrix servers, which can take much time (up to a minute and even more, depending on the number of rooms and the number of users in them). Deleting cache files may help to fix problems such as missing avatars, rooms stuck in a wrong state etc.

## Building

Quaternion source code is hosted at GitHub: https://github.com/QMatrixClient/Quaternion - checking out a certain commit or tag from GitHub (rather than downloading the archive) is the recommended way for packagers.

Quaternion uses libqmatrixclient that is developed in a separate GitHub repo and is fetched as a git submodule. To get all necessary sources:
- if you haven't cloned the Quaternion sources yet:
```
git clone --recursive https://github.com/QMatrixClient/Quaternion.git
```
- if you already have cloned Quaternion, do the following in the top-level directory (NOT in lib subdirectory):
```
git submodule init
git submodule update
```

There are very few tags so far; there will be more, as new versions are released.

### Pre-requisites
- a Linux, MacOS or Windows system (desktop versions tried; mobile Linux/Windows might work too)
  - For Ubuntu flavours - Trusty Tar or later (or a derivative) is good enough; older ones will need PPAs at least for a newer Qt
- a Git client to check out this repo
- CMake (from your package management system or [the official website](https://cmake.org/download/))
- Qt 5 (either Open Source or Commercial), version 5.2.1 or higher as of this writing (check the CMakeLists.txt for most up-to-date information). Qt 5.3 or higher recommended on Windows.
- a C++ toolchain supported by your version of Qt (see a link for your platform at [the Qt's platform requirements page](http://doc.qt.io/qt-5/gettingstarted.html#platform-requirements))
  - GCC 4.8, Clang 3.5.0, Visual C++ 2015 are the oldest officially supported as of this writing

#### Linux
Just install things from the list above using your preferred package manager. If your Qt package base is fine-grained you might want to take a look at `CMakeLists.txt` to figure out which specific libraries Quaternion uses (or blindly run cmake and look at error messages). Note also that you'll need several Qt Quick plugins for Quaternion to work (without them, it will compile and run but won't show the messages timeline). In case of Trusty Tar the following line will get you everything necessary to build and run Quaternion (thanks to `@onlnr:matrix.org`):
```
sudo apt-get install git cmake qtdeclarative5-dev qtdeclarative5-qtquick2-plugin qtdeclarative5-controls-plugin
```
On Fedora 26, the following command should be enough for building and running:
```
dnf install git cmake qt5-qtdeclarative-devel qt5-qtquickcontrols
```

#### OS X
`brew install qt5` should get you Qt5. You may need to tell CMake about the path to Qt by passing `-DCMAKE_PREFIX_PATH=<where-Qt-installed>`

#### Windows
1. Install CMake. The commands in further sections imply that cmake is in your PATH - otherwise you have to prepend them with actual paths.
1. Install Qt5, using their official installer. If for some reason you need to use Qt 5.2.1, select its Add-ons component in the installer as well; for later versions, no extras are needed. If you don't have a toolchain and/or IDE, you can easily get one by selecting Qt Creator and at least one toolchain under Qt Creator. At least Qt 5.3 is recommended on Windows; `windeployqt` in Qt 5.2.1 is not functional enough to provide a standalone installation for Quaternion though you can still compile and run from your build directory.
1. Make sure CMake knows about Qt and the toolchain - the easiest way is to run a qtenv2.bat script that can be found in `C:\Qt\<Qt version>\<toolchain>\bin` (assuming you installed Qt to `C:\Qt`). The only thing it does is adding necessary paths to PATH - you might not want to run it on system startup but it's very handy to setup environment before building. Setting CMAKE_PREFIX_PATH, the same way as for OS X (see above), also helps.

There are no official MinGW-based 64-bit packages for Qt. If you're determined to build 64-bit Quaternion, either use a Visual Studio toolchain or build Qt5 yourself as described in Qt documentation.

### Build
In the root directory of the project sources:
```
mkdir build_dir
cd build_dir
cmake .. # Pass -DCMAKE_PREFIX_PATH and -DCMAKE_INSTALL_PREFIX here if needed
cmake --build . --target all
```
This will get you an executable in `build_dir` inside your project sources. `CMAKE_INSTALL_PREFIX` variable of CMake controls where Quaternion will be installed - pass it to `cmake ..` above if you wish to alter the default (see the output from `cmake ..` to find out the configured values).

### Install
In the root directory of the project sources: `cmake --build build_dir --target install`.

On Linux, `make install` (with `sudo` if needed) will work equally well.

### Package
Packagers are very scarce so far, so please step up and support your favourite system! Notably, we still need a MacOS maintainer - Quaternion sees no actual usage/testing on this platform yet.

#### Flatpak
If you run Linux and your distribution supports flatpak, you can easily build and install Quaternion as a flatpak package:

```
git clone https://github.com/QMatrixClient/Quaternion.git --recursive
cd Quaternion/flatpak
./setup_runtime.sh
./build.sh
flatpak --user install quaternion-nightly com.github.quaternion
```
Whenever you want to update your Quaternion package, do the following from the flatpak directory:

```
./build.sh
flatpak --user update
```

## Troubleshooting

If `cmake` fails with...
```
CMake Warning at CMakeLists.txt:11 (find_package):
  By not providing "FindQt5Widgets.cmake" in CMAKE_MODULE_PATH this project
  has asked CMake to find a package configuration file provided by
  "Qt5Widgets", but CMake did not find one.
```
...then you need to set the right -DCMAKE_PREFIX_PATH variable, see above.

If `cmake` fails with...
```
CMake Error at CMakeLists.txt:30 (add_subdirectory):
  The source directory

    <quaternion-source-directory>/lib

  does not contain a CMakeLists.txt file.
```
...then you don't have libqmatrixclient sources - most likely because you didn't do the `git submodule init && git submodule update` dance.

If you have made sure that your toolchain is in order (versions of compilers and Qt are among supported ones, PATH is set correctly etc.) but building fails with strange Qt-related errors such as not found symbols or undefined references, double-check that you don't have Qt 4.x packages around ([here is a typical example](https://github.com/QMatrixClient/Quaternion/issues/185)). If you need those packages reinstalling them may help; but if you use Qt4 by default you have to explicitly pass Qt5 location to CMake (see notes about CMAKE_PREFIX_PATH in "Building").

If Quaternion starts displaying the message that it couldn't connect to the server and retries more than a couple of times without success, while you're sure you have the network connection - sorry but you might probably have to find an alternative network connection so far. Qt, even the newest one, has issues with losing connectivity on some WiFi networks (and not recovering from that), and libqmatrixclient doesn't have a workaround for this yet.

If Quaternion runs but you can't see any messages in the chat (though you can type them in) - you have a problem with Qt Quick. Most likely, you don't have Qt Quick libraries and/or plugins installed. On Linux, double check "Pre-requisites" above and also see the stdout/stderr logs, they are quite clear in such cases. On Windows and Mac, just open an issue (see "Contacts" in the beginning of this README), because most likely not all necessary Qt parts got copied along with Quaternion (which is a bug).

Especially on Windows, if Quaternion starts up but upon an attempt to connect returns a message like "Failed to make SSL context" - you haven't made sure that SSL libraries are reachable buy the Quaternion binary. See the "OpenSSL on Windows" section above for details.

If you have troubles with dynamic libraries on Windows, [the Dependencies Walker tool aka depends.exe](http://www.dependencywalker.com/) helps a lot in navigating the DLL hell - especially when you have a mixed 32/64-bit environment or have different versions of the same library scattered around. OpenSSL, in particular, is notoriously often dragged along by all kinds of software; and you may have other copies of Qt around which you didn't even know about - e.g., with CMake GUI.

When chasing bugs and investigating crashes, it helps to increase the debug level. Thanks to @eang:matrix.org, libqmatrixclient uses Qt logging categories - the "Troubleshooting" section of `lib/README.md` elaborates on how to setup logging. Note that Quaternion itself doesn't use Qt logging categories yet, only the library does.

## Screenshot
![Screenshot](quaternion.png)
