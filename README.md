# yuki-s-ai

## Codex 本地跨对话记忆插件 · 1.1.2

通过命名确认保存本地对话记忆，支持新对话明确调用，以及 Codex / DSH 通用记忆文件交换。

### 下载与源码

- [完整源码与安装脚本 ZIP](codex-local-memory-1.1.2.zip)（推荐，包含全部目录及测试）
- [标准 npm 格式源码包 TGZ](codex-local-conversation-memory-1.1.2.tgz)
- [文件结构与关键接口](FILES.md)
- [Codex ↔ DSH 端对端记忆传递与验证指南](END_TO_END_MEMORY_GUIDE.md)
- [更新记录](CHANGELOG.md)
- [SHA-256 校验值](SHA256SUMS.txt)

本次采用 GitHub 网页直接发布，完整源码目录保存在 ZIP 中。TGZ 是源码分发包，不是可直接交给 DSH 安装的插件；Codex 请使用 ZIP 内的安装脚本。

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
- 跨端：保存后自动生成 portable.memory.json；DSH 需安装兼容插件 2.1.0 或更新版后导入。

本版打开确认页时检查附件可读性；无法读取、不是文件或单个超过 50 MiB 的附件默认不勾选，保留原因。可读取附件默认全选。最多 250 条候选要点。

### 数据与验证

无后台云同步，不上传保存的记忆文件。用户明确调用时，所选摘要进入当前模型上下文。仓库和安装包不包含用户记忆、个人附件、运行令牌或本机配置。

Codex 侧 23 项自动测试通过；合成数据浏览器验收确认不可读附件默认未选中并成功保存。跨模型数据兼容不代表所有真实模型已端到端测试。

许可证：MIT。源码解压后可运行 `node --test plugins/local-conversation-memory/tests/*.test.mjs` 复测。
