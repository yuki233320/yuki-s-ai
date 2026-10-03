# 源码目录与关键接口

此清单覆盖发布包所有业务文件；实际文件 SHA256 见同目录 SHA256SUMS.txt。

```
codex-memory-marketplace/
├─ .agents/plugins/marketplace.json    本地目录；name、source.path、安装策略
├─ install.ps1                        安装、备份原配置、生成本机运行时与可选钩子
├─ README.md                          使用、安装、数据边界、限制与卸载
├─ FILES.md                           本清单
└─ plugins/local-conversation-memory/
   ├─ .codex-plugin/plugin.json       Codex 官方兼容清单；skills 入口、版本、界面名称
   ├─ package.json                   ESM、Node >=20、测试命令、npm 包文件范围
   ├─ LICENSE                        MIT
   ├─ README.md                      插件使用说明
   ├─ skills/local-memory/
   │  ├─ SKILL.md                    自然语言触发、确认保存、明确调用与异常处理
   │  └─ agents/openai.yaml           UI 名称、默认提示、隐式技能选择
   ├─ scripts/
   │  ├─ memory.mjs                  CLI：prepare/import/export/status/list/recall/delete/capacity/dismiss/doctor
   │  └─ hook.mjs                    handleHook：UserPromptSubmit / Stop，失败不打断
   ├─ src/
   │  ├─ intent.js                   memoryIntent：本地保守规则识别；复用 DSH 验证集
   │  ├─ extract.js                  extractPoints / mergePoints：原文摘录、来源、分类
   │  ├─ resources.js                resourcesFrom：当前消息明确路径提取
   │  ├─ transcript.mjs              findTranscript / readTranscript / measuredCapacity
   │  ├─ storage.mjs                 saveSelection / recallMemory / listMemories / deleteMemory
   │  ├─ portable.mjs                通用格式 v1：验证、导入选择、附件封装与 SHA-256
   │  ├─ probe-file.mjs              打开页面前检查本地附件可读性，不读取正文
   │  └─ server.mjs                  startReview：仅 127.0.0.1 的临时确认服务
   ├─ ui/
   │  ├─ index.html                  命名、要点、附件默认全选、取消/保存
   │  ├─ ui.js                       表单校验、独立勾选、令牌与本机接口调用
   │  └─ ui.css                      中文自适应界面样式
   └─ tests/
      ├─ memory.test.mjs             15 项存储、接口、隔离、用量、意图和钩子测试
      ├─ portable.test.mjs           3 项往返、格式与完整性、路径安全回归
      ├─ availability.test.mjs       缺失、目录、超限附件可读性回归
      ├─ resources-regression.test.mjs 网址和错误文本不误识别为附件
      └─ intent-corpus.json          48 正样本、72 负样本及数据来源说明
```

仅在安装目标生成（发布源包不包含任何用户设置或记忆）：

- `plugins/local-conversation-memory/runtime.json`：本机 Node 路径 `node`、本地存储路径 `dataRoot`。
- `plugins/local-conversation-memory/hooks/hooks.json`：两个待用户审阅信任的钩子，15 秒超时。
- `pre-install-config.toml`：安装前本机配置副本，可能含敏感设置，始终只留本机。

安装器通过 Codex CLI 建立独立的已安装缓存，不复制任何用户记忆进插件包。安装后缓存目录由 CLI 返回，不假设固定名为 local 或特定版本。
