# Modifications in this fork

This repository, https://github.com/kuanyui/XADMaster, is a fork of XADMaster
(https://github.com/MacPaw/XADMaster), the library of The Unarchiver and of its command-line
tools `unar` and `lsar`. It builds `unar` and `lsar` for macOS (Apple silicon and Intel), and
tries to build them for Windows.

Its changes to XADMaster:

- `XADUnarchiver.m` (2026-10-29): with GNUstep under MinGW (Windows), `-fileSystemRepresentation`
  returns a UTF-16 string. So the placeholder file of an unsafe link is removed with `_wunlink()`,
  as `CSFileHandle.m` opens files with `_wfopen()`. Before, `unlink()` got this UTF-16 string as a
  `char` string, and would have removed a file named after the first character of the path. Newer
  versions of clang reject that code.
- `XADStringWindows.m` (2026-10-29): EUC-JP uses the Windows code page 20932 instead of 51932.
  `MultiByteToWideChar()` cannot convert from 51932, so a file name in EUC-JP became escaped
  ASCII, for example `%a4%ac` instead of が.
- `unar.m` and `lsar.m` (2026-10-31): the version (`VERSION_STRING`) gets the suffix
  `-kyYYYYMMDD`, where `YYYYMMDD` is the date of a release of this fork. For example,
  `v1.10.7-ky20261031` is the release of 2026-10-31, based on `v1.10.7`. This suffix identifies
  this fork in the output of `unar -version` and `lsar -version`, and in the names of the files
  of a release.
- `.github/workflows/build.yaml`: builds `unar` and `lsar`, with UniversalDetector
  (https://github.com/MacPaw/universal-detector) at its last commit. For a tag whose name is the
  version without its `v` (for example `1.10.7-ky20261031`), the workflow also puts both packages
  and their source code on the release page of the tag.
  - macOS: universal binaries for Apple silicon (arm64) and Intel (x86_64), for macOS 12 or later.
    Before the build, the workflow links `lsar` to libc++ instead of libstdc++, which the macOS SDK
    no longer has. This change of `XADMaster.xcodeproj` is made in the workflow only.
    The package `unar-VERSION-macos-universal.zip` has `unar`, `lsar`, LICENSE and this file.
  - Windows (an experiment): built in MSYS2 (CLANG64), with GNUstep Base 1.31.1 that the workflow
    builds without ICU, TLS, XML and XSLT, and with two later fixes of GNUstep: commit bace6f54 (its
    configure test of constant strings) and https://github.com/gnustep/libs-base/pull/809 (no
    `__block` on an instance variable). Both changed files of GNUstep Base say so in a comment.
    The programs run on Windows 10 or later (x64), which has the C runtime that they use (UCRT).
    The package `unar-VERSION-win64.zip` has `unar.exe`, `lsar.exe`, the DLLs that they need,
    LICENSE, this file, `licenses/` (the license files of the DLLs), and THIRD-PARTY.txt (the
    package, version and license of each DLL, and for an LGPL or GPL one, where its source code is
    in `unar-VERSION-src.tar.xz`).
  - The source code of both release packages is in `unar-VERSION-src.tar.xz`, a separate file of
    the same release (https://github.com/kuanyui/XADMaster/releases): XADMaster and
    UniversalDetector at the commits built (with this workflow, which shows how they and GNUstep
    Base are built), GNUstep Base as built, and the MSYS2 source package of each DLL under the LGPL
    or the GPL. The LGPL allows the source code to be a separate download from the same place as
    the programs (sections 4 and 6d).
- This file.
