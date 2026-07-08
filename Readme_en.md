# OmniPascal Chinese Path Fix

[中文说明](Readme.md) | [English](Readme_en.md) | [OmniPascal](Readme_origin.md)

## Acknowledgements and Notice

First of all, many thanks to the original OmniPascal author and contributors. OmniPascal provides very useful Delphi and Free Pascal support in VS Code, including completion, navigation, symbol indexing, and syntax highlighting. This repository exists only because of that original work.

This is not the official OmniPascal project, and I do not intend to infringe on the original author's copyright or any other rights. I unpacked and patched the VSIX only because I encountered a Chinese path encoding issue in daily use, while the original extension appears to be no longer maintained.

If the original author, rights holder, or maintainers consider this repository inappropriate, please contact me. I will explain, adjust, or remove this repository at any time.

This is an unofficial patched build based on the unpacked `Wosi.omnipascal 0.19.0` VSIX. It exists mainly to fix Chinese path mojibake issues that I encountered when using OmniPascal in a Chinese Windows environment.

If you do not have Chinese path issues, or if you are not using a Chinese locale/environment, you should probably keep using the original OmniPascal extension. This repository is a compatibility patch for a specific problem, not an official continuation of the original project.

## Why This Exists

While using OmniPascal, I ran into a path encoding problem: code navigation worked normally when files were under ASCII-only paths, but failed when the path contained Chinese characters.

For example:

- `D:/Test/Main.pas` worked normally
- `D:/测试/Main.pas` could produce garbled paths and fail to navigate

After investigation, the issue turned out to be in the communication between the VS Code client and the bundled `OmniPascalServer`. Some server responses may contain local ANSI/GBK encoded path bytes, while the client originally decoded those responses as UTF-8. As a result, Chinese directory names became mojibake and VS Code tried to open a non-existent file path.

The original extension has not been maintained for a long time, so I had to unpack the VSIX and patch the client-side JavaScript.

## What Was Changed

This build keeps the original OmniPascal behavior and only applies a small compatibility fix around path handling:

- Handles server responses where `Content-Length` does not match the real UTF-8 byte length.
- Falls back to `gb18030` decoding when a server response contains invalid UTF-8 bytes, which helps with Chinese Windows path strings.
- Resolves server-returned file paths before passing them to VS Code, with a workspace-based fallback.
- Bumps the extension version to `0.19.3` to avoid VS Code reusing a cached installation of the same version.

## Installation

Install the VSIX from this repository:

```powershell
code --install-extension .\omnipascal-0.19.3-gbk-path-fix.vsix --force
```

After installation, run `Developer: Reload Window` and confirm that the installed extension version is `0.19.3`.

## Who Should Use This

This patched build may help if:

- You use OmniPascal on Chinese Windows.
- Your Delphi / Free Pascal project path contains Chinese directory names.
- Code navigation, diagnostics, or symbol resolution fail because of garbled Chinese paths.

You probably do not need it if:

- Your project paths are ASCII-only.
- You are not using a Chinese locale/environment.
- The original OmniPascal extension already works well for you.

## Original OmniPascal

If you do not need this Chinese path fix, please use the original extension:

- OmniPascal website: https://www.omnipascal.com
- VS Code Marketplace: https://marketplace.visualstudio.com/items?itemName=Wosi.omnipascal
- Original issue tracker: https://bitbucket.org/Wosi/omnipascalissues/issues

## Notice

This repository is not the official OmniPascal project. It is only a compatibility patch for Chinese path handling. The original functionality and bundled server binaries still come from the original extension package.

