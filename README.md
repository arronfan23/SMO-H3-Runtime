# SMO-H3-Runtime

> **本仓库已迁移。** 运行包、环境清单与第三方声明均已合并到主仓库 [SMO-H3-Studio](https://github.com/arronfan23/SMO-H3-Studio)。新下载请使用主仓库的 [runtime-v1 Release](https://github.com/arronfan23/SMO-H3-Studio/releases/tag/runtime-v1)。本仓库保留仅用于兼容已分发的旧版本链接，后续不再更新。

SMO.H3 Studio 的 SelfLift Spark 运行包分块、环境清单与可复现打包脚本。

<p>
  <a href="https://github.com/arronfan23/SMO-H3-Runtime/releases/tag/runtime-v1"><img alt="运行环境 runtime-v1" src="https://img.shields.io/badge/runtime-v1-2f7ea6" /></a>
  <a href="./LICENSE"><img alt="许可证 MIT" src="https://img.shields.io/badge/license-MIT-green" /></a>
  <img alt="平台 Windows" src="https://img.shields.io/badge/platform-Windows-blue" />
</p>

本仓库不是 MiniMax 官方整合包，也不代表 ComfyUI、SelfLift 或模型发布方提供官方支持。运行包中的上游组件保留原有许可证；本仓库的 MIT 许可证仅适用于 SMO 脚本与文档。

## 下载

SMO.H3 Studio 首次启动时会按硬件自动下载对应分块；一般用户无需手动下载。手动下载请使用 [runtime-v1 Release](https://github.com/arronfan23/SMO-H3-Runtime/releases/tag/runtime-v1)：

| 文件 | 说明 | 大小 |
| --- | --- | --- |
| [selflift-spark.json](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/selflift-spark.json) | 环境与模型清单（SHA256、大小、来源） | 7.2 KB |
| [SM89 part1](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM89_CUDA130_NoModels.zip.part1) · [part2](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM89_CUDA130_NoModels.zip.part2) · [part3](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM89_CUDA130_NoModels.zip.part3) | RTX 30 / 40 系运行包（分块） | 4.55 GB |
| [SM120 part1](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM120_CUDA130_NoModels.zip.part1) · [part2](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM120_CUDA130_NoModels.zip.part2) · [part3](https://github.com/arronfan23/SMO-H3-Runtime/releases/download/runtime-v1/ComfyUI_SelfLift_Spark_SM120_CUDA130_NoModels.zip.part3) | RTX 50 系运行包（分块） | 4.55 GB |

同一运行包的三个分块需全部下载后按顺序合并，并按清单核对整包 SHA256 后使用。配套桌面应用见 [SMO-H3-Studio](https://github.com/arronfan23/SMO-H3-Studio)。

## 硬件与运行包

| NVIDIA GPU | CUDA 计算能力 | 运行包 |
| --- | --- | --- |
| RTX 30 / 40 系 | SM89（8.6 / 8.9） | `ComfyUI_SelfLift_Spark_SM89_CUDA130_NoModels.zip` |
| RTX 50 系 | SM120（12.0） | `ComfyUI_SelfLift_Spark_SM120_CUDA130_NoModels.zip` |

最低硬件基线为 NVIDIA RTX 30 系、8 GB 显存和 32 GB 系统内存。8 GB 显存、RTX 30 系及 BF16 路径均为未实测候选，不保证能够完成所有工作流；清单匹配不等于兼容性认证。

## 下载与校验

- 运行包不包含模型。模型仍由 [Hugging Face](https://huggingface.co) 上的实际发布方提供，来源与 SHA256 记录在 `manifests/selflift-spark.json`。
- SMO.H3 Studio 安装器按硬件选择一个运行包，并按需下载清单中的运行包分块和模型，不预置整套模型文件。
- 下载后逐块核对字节数与 SHA256，再核对合并 ZIP 的整体字节数与 SHA256；校验失败时不会静默使用损坏文件。
- 安装器不实现账号登录，也不要求在应用中填写 GitHub 或 Hugging Face 凭据。
- `runtime-v1` 的下载 URL 已固定在清单中。Release 资产的发布是独立审核步骤，本仓库的打包脚本不会自动上传文件或创建 Release。

## 仓库内容

- `manifests/selflift-spark.json`：SM89、SM120 运行包与 10 个模型文件的下载及校验元数据。
- `scripts/package-runtime.ps1`：使用 .NET `FileStream` 流式切分 ZIP，并计算分块与整包 SHA256。
- `scripts/build-environment-manifest.mjs`：核对 Hugging Face 发布方元数据并生成环境清单。
- `licenses/ComfyUI-LICENSE`：运行包内 ComfyUI 的上游许可证原文。
- `THIRD_PARTY_NOTICES.md`：第三方组件与模型来源说明。

`runtime-packs/`、ZIP 分块、模型和临时下载文件均被 Git 忽略，不应提交到仓库。

## 本地复现

需要 Windows、PowerShell 5.1 或更高版本，以及 Node.js 20 或更高版本。准备两份原始无模型 ZIP 后，在仓库根目录运行：

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\scripts\package-runtime.ps1 `
  -SM89Zip <SM89-ZIP路径> `
  -SM120Zip <SM120-ZIP路径> `
  -OutputRoot .

node .\scripts\build-environment-manifest.mjs `
  --package-metadata .\runtime-packs\metadata.json `
  --source-manifest .\manifests\selflift-spark.json `
  --output .\manifests\selflift-spark.json
```

默认分块上限为 1,900,000,000 字节。脚本只读取原 ZIP，不修改模型、工作流或启动脚本。清单生成器会访问 Hugging Face 的公开仓库元数据 API；网络失败、文件缺失、大小或 SHA256 不匹配都会终止生成。

## 许可证

本仓库的 SMO 脚本与文档使用 [MIT 许可证](./LICENSE)。第三方组件和模型不适用该许可证，详见 [THIRD_PARTY_NOTICES.md](./THIRD_PARTY_NOTICES.md)。
