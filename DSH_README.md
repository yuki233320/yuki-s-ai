# DSH 本地跨对话记忆插件

**唯一当前版本：`2.1.1+dsh-local.4`。对应应用：DeepSeek Harness `0.2.0-rc.2`（Windows 桌面 / Web），宿主 Node.js 22+。**

DSH 与 Codex 分别维护版本。此版本是维护者指定的唯一最新 DSH 标准，不以 Codex 版本号或先前开发包的数字排序决定。未合并先前 DSH 2.1.2 的功能。

## 下载

- [可直接安装的 TGZ](dsh-cross-session-memory-2.1.1%2Bdsh-local.4.tgz)
- [完整源码与安装包 ZIP](dsh-cross-session-memory-2.1.1%2Bdsh-local.4-source.zip)
- [SHA-256 清单](SHA256SUMS.txt)

ZIP 保持用户提供文件的原始字节，仅分发文件名改为英文。TGZ 直接从 ZIP 的 `dist/` 提取，未改动、未重建。源码与 TGZ 的版本声明相同，TGZ 中 33 个文件均与 ZIP 对应文件一致。

## 安装

1. 下载 **DSH TGZ** 到运行 DSH 的本机，不解压 TGZ。
2. 在 DSH 的「插件 → 添加插件」中，将实际 TGZ 绝对路径填入安装来源；输入框里不额外加引号。
3. 安装、启用，并按提示重启 DSH。同一包名已有其他版本时，以本页的完整版本为准；重启后核对安装结果。

CLI 示例（先替换为实际路径，带空格时保留双引号）：

```powershell
dsh plugin --profile desktop add "C:\插件目录\dsh-cross-session-memory-2.1.1+dsh-local.4.tgz"
```

使用桌面应用配套的 CLI，并先完全退出桌面应用。若 `dsh` 不在 PATH，使用配套 `dsh.cmd` 的完整路径；不要因此安装 npm 同名包。Web profile 将 `desktop` 改为 `web`。远程 Web 场景的安装来源必须是宿主服务器能读取的路径。

ZIP 内历史 README 的某些安装示例仍写旧 TGZ 名称，应以本页及 `dist/README.md` 的实际文件名为准；原包保持不变。

## 当前行为

- 命名必填，确认后本地保存；自然语言可触发命名面板。
- 本地候选上限 **500 条**；跨端通用文件仅保留所选要点中的**最近 250 条**，并提示截断数量。
- “同时写出跨端通用记忆文件”默认勾选；取消后本次不生成该文件，可用 `/memory-export ID` 补导出。
- 保存成功短提示会消失，路径在输入区“记忆地址”悬停卡片中查看与复制。
- 附件仍默认全选，本版本没有不可读附件预检查；读取失败时需恢复文件或取消对应附件。
- 通用格式与 Codex 1.1.2 兼容；详见 [双端传递指南](END_TO_END_MEMORY_GUIDE.md) 和 [验证范围及结果](INTEROP_VALIDATION.md)。

## ZIP 内容

| 路径 | 用途 |
| --- | --- |
| `package.json` | 包名、完整版本、Node 要求、宿主依赖和构建测试命令 |
| `cordis.patch.yml` | DSH 插件入口配置 |
| `lib/` | 直接运行的宿主、投影及客户端构建文件 |
| `src/` | 命名界面、提炼、资源、存储、上下文及通用格式源码 |
| `tests/` | 离线样例、存储、地址卡片、选项、上限及宿主测试 |
| `scripts/` | 构建、路径解析和安装辅助脚本 |
| `locale/` | 中英文描述 |
| `dist/` | 原始 TGZ、安装说明及 TGZ 校验值 |
| `FILES.txt` | 逐文件功能与接口说明 |
| `README.md` | 包内完整功能说明，包含历史修订记录 |
| `LICENSE`、`THIRD_PARTY_NOTICES.txt` | MIT 与内嵌依赖许可 |

源码与安装包不包含用户的真实记忆库。安装包发布到 GitHub 不会上传用户保存的记忆文件。
