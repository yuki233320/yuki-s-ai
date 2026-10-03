# yuki-s-ai

Codex 与 DSH 的本地跨对话记忆插件，两端独立开发、独立编号，通过通用记忆文件互操作。

## 当前版本与对应应用

| 应用端 | 唯一当前插件版本 | 对应宿主 / 运行环境 | 安装入口 |
| --- | --- | --- | --- |
| Codex | **1.1.2** | Windows Codex；本机 CLI 版本记录为 **0.159.0-alpha.12.1**，桌面应用版本未取得；Node.js 20+ | 下方 Codex ZIP 与安装脚本 |
| DSH | **2.1.1+dsh-local.5** | **DeepSeek Harness 0.2.0-rc.2**；宿主 Node.js 22+ | [DSH 安装与版本说明](DSH_README.md) |

DSH `2.1.1+dsh-local.5` 是本仓库唯一最新 DSH 版本标准。先前文档提及的 DSH 2.1.2 不再作为当前发布依据；本次基于 local.4 独立迭代，新增保存失败后取消勾选失败项、等待再次确认；没有合并旧 2.1.2 功能。Codex 与 DSH 的插件版本无需对齐。两端只约定通用文件 `local-conversation-memory` 格式版本 1；当前兼容性结论见 [验证报告](INTEROP_VALIDATION.md)。本机 CLI 版本是环境记录，不表示已验证所有 Codex 桌面版本或联网模型。

## DSH 下载

- [DSH 完整源码与安装包 ZIP](dsh-cross-session-memory-2.1.1%2Bdsh-local.5-source.zip)（包含 local.5 完整源码、构建产物与安装包）
- [DSH 可直接安装 TGZ](dsh-cross-session-memory-2.1.1%2Bdsh-local.5.tgz)（已构建，包内完整版本为 2.1.1+dsh-local.5）
- [DSH 安装、功能与文件说明](DSH_README.md)
- [Codex ↔ DSH 端对端传递指南](END_TO_END_MEMORY_GUIDE.md)
- [各端版本索引](VERSIONS.json)

DSH local.5：保存失败时自动取消勾选可定位的失败附件或要点，保留其他选择并等待用户再次点击保存；存储权限、重名等错误不乱改选择。

DSH 本地最多 500 条候选；跨端文件最多最近 250 条。DSH 弹窗中“同时写出跨端通用记忆文件”默认勾选，取消后仍可用 `/memory-export ID` 补导出。保存路径在“记忆地址”悬停卡片中查看。

## Codex 本地跨对话记忆插件 · 1.1.2

通过命名确认保存本地对话记忆，支持新对话明确调用，以及 Codex / DSH 通用记忆文件交换。

### 下载与源码

- [完整源码与安装脚本 ZIP](codex-local-memory-1.1.2.zip)（推荐，包含全部目录及测试）
- [标准 npm 格式源码包 TGZ](codex-local-conversation-memory-1.1.2.tgz)
- [文件结构与关键接口](FILES.md)
- [Codex ↔ DSH 端对端记忆传递与验证指南](END_TO_END_MEMORY_GUIDE.md)
- [更新记录](CHANGELOG.md)
- [SHA-256 校验值](SHA256SUMS.txt)

Codex 完整源码目录保存在 Codex ZIP 中。上方 Codex TGZ 是源码分发包，不能交给 DSH 安装；Codex 请使用其 ZIP 内的安装脚本。DSH 使用单独的 DSH TGZ。

### 安装

适用于 Windows 本地 Codex，需要本机已有 Codex CLI 与 Node.js 20+。解压 ZIP，在包含 install.ps1 的目录执行：

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File ".\install.ps1"
```

若 Codex / Node 不在 PATH，给脚本传入 `-CodexPath` 与 `-NodePath` 的完整路径。可用 `-DataRoot` 指定记忆存储目录。已有安装目录不会被覆盖；重新安装可用 `-InstallRoot` 选择新目录。其他参数、可选钩子信任及完整限制见 ZIP 内 README.md。

### 使用

- 保存：“记住这段对话”，填写名称并在页面确认。
- 调用：“调用本地记忆「名称」”。
- 导入：“使用本地记忆插件导入这个 portable.memory.json 文件”，提供本地路径并确认。
- 跨端：保存后自动生成 portable.memory.json；DSH 使用本仓库指定的 2.1.1+dsh-local.5 后导入。

本版打开确认页时检查附件可读性；无法读取、不是文件或单个超过 50 MiB 的附件默认不勾选，保留原因。可读取附件默认全选。最多 250 条候选要点。

### 数据与验证

无后台云同步，不上传保存的记忆文件。用户明确调用时，所选摘要进入当前模型上下文。仓库和安装包不包含用户记忆、个人附件、运行令牌或本机配置。

Codex 侧 23 项自动测试通过；合成数据浏览器验收确认不可读附件默认未选中并成功保存。跨模型数据兼容不代表所有真实模型已端到端测试。

许可证：MIT。源码解压后可运行 `node --test plugins/local-conversation-memory/tests/*.test.mjs` 复测。
