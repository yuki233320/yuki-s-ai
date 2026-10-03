# 当前版本的互操作与历史兼容验证

验证日期：2026-10-03。插件组合：**Codex 1.1.2 ↔ DSH 2.1.1+dsh-local.6**；DSH 宿主模块 **0.2.0-rc.2**；测试 Node.js **24.19.0**。Codex CLI 既有记录为 0.159.0-alpha.12.1，桌面版本未取得。

## 实测范围

使用模拟对话与附件，在隔离测试目录运行真实存储代码和真实宿主模块。发布包不包含用户记忆、会话日志、个人附件或运行配置。没有联网模型请求，不据此声称所有桌面版本或真实模型均已端到端通过。

| 检查 | local.6 实测结果 |
| --- | --- |
| 发布包 | ZIP CRC 通过；TGZ 中版本完整，文件与对应源码逐字节一致；校验值见 SHA256SUMS.txt |
| Node 回归 | 43 项通过；覆盖存储、附件、地址卡片、命令投影、导入、失败恢复及过期恢复结果拒绝 |
| Python 历史恢复工具 | 4 项通过；默认不写入、原始备份、只改选中事件、其他压缩帧保留、重复执行及异常数据拒绝 |
| 官方宿主集成 | 已构建 lib/index.js 在 0.2.0-rc.2 模块中完成保存、跨会话召回、导入命名投影、确认落库、列表与导出 |
| 独立进程冷启动 | 真实宿主先记录失败→用户第二次确认成功，全部为官方已知类型；写为 Zstandard JSONL 后，由独立新进程通过官方 validateStoredEvents，恢复消息、重放失败状态与成功地址卡片 |
| Codex → DSH → Codex | 250 条要点及可用来源文本、原始身份/来源端/时间、遗漏数量、二进制附件字节和哈希保持；两端落库及本地召回通过 |
| DSH → Codex → DSH | 本地保存 500 条，通用文件保留最近 250 条，遗漏计数 250；交换内容、附件和原始身份保持 |
| 重复保护 | 同一原始记忆向同一 DSH 项目重复导入被拒绝 |

旧日志恢复只给已确认的插件辅助通知增加顶层 `ignorable: true`；不是宿主白名单修改，也不是任意未知事件跳过器。升级不自动恢复旧日志，见 [恢复说明](HISTORY_RECOVERY.md)。

## 已知差异

DSH `/memory-export` 会重建 `extraction`，省略 Codex 可选的 `originalMessages` 统计字段。内容字段比较通过，并单独断言此差异；这不是完整 JSON 对象逐字段无损往返的结论。

DSH 本地上限 500 条，跨端共享格式仍是 250 条。要全部传递，请分成每份不超过 250 条的独立命名记忆。

## 复测

将两个源码 ZIP 解压到以下目录，内部直接包含各自源码，不要多套一层目录：

```text
验证目录/
├─ verify-interop.mjs
├─ codex-local-memory/plugins/local-conversation-memory/...
└─ dsh-local-memory/
   ├─ src/...
   ├─ lib/...
   ├─ scripts/...
   └─ tests/...
```

下载 [verify-interop.mjs](verify-interop.mjs)，用 Node.js 22+ 运行：

```powershell
node .\verify-interop.mjs
node --test .\dsh-local-memory\tests\*.test.mjs
```

双端脚本只创建并清理它拥有的模拟测试目录，不读取已安装插件的记忆库。宿主集成需要源码 package.json 声明的开发依赖；在 DSH 源码目录执行：

```powershell
pnpm install
pnpm run build
node tests/host.integration.mjs
node tests/interop.host.mjs
node tests/compatibility.host.mjs write .\compatibility-fixture
node tests/compatibility.host.mjs read .\compatibility-fixture
```

`write` 要使用未生成过该夹具日志的目录，`read` 必须另启一个 Node 进程。此验证使用官方历史校验函数及会话重建接口，程序驱动宿主模块，不等于人工桌面界面验收。冷启动测试需 Node 具备 `node:zlib` 的 Zstandard API（本次使用 24.19.0），此要求只影响该开发测试，不影响插件 Node 22+ 运行要求。

恢复工具测试另需 Python 3.9+ 和 `zstandard`：

```powershell
python tests/history_repair_test.py
```

用户真实端对端操作方法见 [传递指南](END_TO_END_MEMORY_GUIDE.md)。
