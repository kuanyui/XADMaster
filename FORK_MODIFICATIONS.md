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
- `.github/workflows/build.yaml`: builds `unar` and `lsar`, with UniversalDetector
  (https://github.com/MacPaw/universal-detector) at its last commit.
  - macOS: universal binaries for Apple silicon (arm64) and Intel (x86_64), for macOS 12 or later.
    Before the build, the workflow links `lsar` to libc++ instead of libstdc++, which the macOS SDK
    no longer has. This change of `XADMaster.xcodeproj` is made in the workflow only.
    The package `unar-macos-universal.zip` has `unar`, `lsar`, LICENSE, this file, and
    `unar-src.zip`: the source code of XADMaster and UniversalDetector at the commits built.
  - Windows (an experiment): built in MSYS2 (CLANG64), with GNUstep Base 1.31.1 that the workflow
    builds without ICU, TLS, XML and XSLT, and with two later fixes of GNUstep: commit bace6f54 (its
    configure test of constant strings) and https://github.com/gnustep/libs-base/pull/809 (no
    `__block` on an instance variable). Nothing is packed yet.
- This file.
