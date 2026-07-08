# OmniPascal 中文路径修复版

这是基于 `Wosi.omnipascal 0.19.0` VSIX 解包后的非官方修复版本，主要用于解决我在中文 Windows 环境中使用 OmniPascal 时遇到的中文路径乱码问题。

如果您在使用过程中没有遇到中文路径问题，或者您不是中文环境用户，建议继续使用原版 OmniPascal。本仓库只是针对特定使用场景的兼容性修补，不是原项目的正式续作。

## 为什么会有这个版本

我在实际使用 OmniPascal 时遇到了一个很棘手的问题：当 Pascal 文件位于包含中文目录名的路径下时，代码跳转会失败。

例如：

- `D:/Test/Main.pas` 可以正常跳转
- `D:/测试/Main.pas` 可能出现乱码路径并跳转失败

进一步排查后发现，OmniPascal 的 VS Code 端和内置 `OmniPascalServer` 之间通信时，服务端在某些响应里会返回带有本地 ANSI/GBK 字节的路径，而客户端原本按 UTF-8 解析这些响应。这样中文目录名会变成乱码，VS Code 最终尝试打开一个不存在的路径。

原扩展已经长期停止维护，所以我不得不解包 VSIX，对客户端 JavaScript 代码做兼容性修补。

## 修改内容

本修复版保留原 OmniPascal 的主体功能，只针对中文路径相关问题做了小范围修改：

- 修复 `Content-Length` 与实际 UTF-8 字节长度不一致时导致 JSON 响应被截断的问题。
- 在服务端响应体出现非法 UTF-8 字节时，回退使用 `gb18030` 解码，以兼容中文 Windows 环境下的路径返回。
- 在 VS Code 打开服务端返回路径前，增加路径解析与工作区路径兜底逻辑。
- 将扩展版本号调整为 `0.19.3`，避免 VS Code 安装同版本 VSIX 时使用旧缓存。

## 安装

可以直接安装仓库中的 VSIX：

```powershell
code --install-extension .\omnipascal-0.19.3-gbk-path-fix.vsix --force
```

安装后建议执行一次 `Developer: Reload Window`，并确认扩展版本为 `0.19.3`。

## 适用人群

适合：

- 在 Windows 中文环境中使用 OmniPascal。
- Delphi / Free Pascal 工程路径中包含中文目录。
- 遇到代码跳转、诊断、符号定位等功能因为中文路径乱码而失败。

不一定需要：

- 工程路径全是英文或 ASCII。
- 非中文系统环境。
- 原版 OmniPascal 在您的机器上已经稳定工作。

## 原版 OmniPascal

如果您不需要这个中文路径修复，建议使用原版：

- OmniPascal 官网：https://www.omnipascal.com
- VS Code Marketplace：https://marketplace.visualstudio.com/items?itemName=Wosi.omnipascal
- 原问题反馈地址：https://bitbucket.org/Wosi/omnipascalissues/issues

## 说明

本仓库不是 OmniPascal 官方项目，只是一个面向中文路径问题的兼容性修补版本。所有原始功能和二进制服务端仍来自原扩展包。

英文说明见：[Readme_en.md](Readme_en.md)

