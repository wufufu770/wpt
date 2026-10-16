# wpt — 授权渗透测试 Skill（pentest）

> 本仓库是 SKILL-DUO 双技能体系中 **pentest skill 的独立发行版**（自包含副本）。姊妹仓库 [wufufu770/wtf](https://github.com/wufufu770/wtf) 为 CTF 解题 skill。

## 用途

为任何支持 SKILL.md 约定（Anthropic Agent Skills 规范 / agentskills.io 事实标准）的 Agent 提供一套**授权渗透测试**知识底座：五阶段流程路由（recon → enum → exploit → postexp → report）+ scope 四层纵深门槛 + REFLIGHT 经验回流协议。

**适用前提：必须有明确授权（授权书 / RoE / scope 文件）。skill 首道门槛 GATE-0 即核验 scope 四要素（目标、授权、时限、禁区），未授权目标一律拒绝。**

## 目录结构

```
wpt/
├── pentest/                  # skill 本体（加载单元）
│   ├── SKILL.md              # 索引 + GATE-0 + 阶段路由 + 收尾回流
│   └── references/           # 7 份：五阶段深化 + scope-authorization（L1/L4）+ scope-hook-spec（L2）
├── shared/                   # 双 skill 共用层（本仓库为自包含副本，见下方同步义务）
│   ├── REFLIGHT.md           # 回流协议：五态状态机 + 裁决词 + 复跑记录
│   └── tools/                # 工具用法唯一真相源（6 份 + INDEX 路由表）
├── tests/
│   ├── trigger-regression.md # 触发回归用例（20 条；P-01~P-05 与全部负例适用本仓库）
│   └── reflight-static-check.py  # REFLIGHT 静态校验
├── PORTABILITY.md            # 跨 Agent 通用性说明
└── README.md
```

## 加载方式

- **形态 A（推荐）**：整个仓库克隆后以目录树为单位注册/加载，`pentest/SKILL.md` 中的 `../shared/...` 相对路径直接生效。
- **形态 B（单目录装载）**：将 `pentest/` 目录单独拷入仅支持单 skill 目录的运行时——此时 `../shared/` 引用会悬空，必须先将 `shared/` 内容并入 `pentest/` 内并同步修正引用路径（自包含降级，详见 PORTABILITY.md §2）。

## 触发场景

触发逻辑全部写在 `pentest/SKILL.md` 的 description 字段内（无平台专有路由依赖）。典型触发话术：授权渗透测试、bug bounty 项目（RoE/scope）、内网授权演练、自有测试机提权验证、靶场全流程演练+渗透报告。干扰排除：漏洞新闻解读、CVE 复述、爬虫开发、恶意样本家族分析等非动手授权测试场景**不触发**。回归用例见 `tests/trigger-regression.md`。

## shared/ 同步义务（形态 B：复制 + 重新同步）

本仓库的 `shared/` 是从 SKILL-DUO 源项目**复制**而来的自包含副本，与姊妹仓库 [wtf](https://github.com/wufufu770/wtf) 的 `shared/` 保持同源（当前字节一致）。依据 `shared/tools/INDEX.md` 的去重维护规则：

- **任一仓库对 `shared/` 的修改（含 REFLIGHT.md 与 tools/ 任一文件），必须同步回另一个仓库**，否则两仓库共享层漂移；
- 同步校验方法：对两仓库 `shared/` 目录做 `diff -r`，应零差异；
- 上游合并方向：SKILL-DUO 源项目（如仍在维护）→ 本仓库与 wtf 仓库。

## 版本记录

- **v0.3（2026-10-16）**：初始发布（cd6ffb7）——pentest skill 五阶段 + scope 四层纵深 + REFLIGHT 协议 + shared 层 20 条工具条目。
- **v0.4（2026-10-16）**：Cairn/CTF-Agent 前沿设计借鉴（源自《reArtex 启发系列二》调研 5 条落点）——① scope-hook-spec.md 新增规则族 2「重复调用去重」循环检测（签名滑窗 12 条，≥3 warn / ≥5 break exit 2，借鉴 verialabs/ctf-agent loop_detect.py）；② shared/REFLIGHT.md §1 增死锁条款（同一假设连续 FAIL ≥3 禁止原路复跑）；③ §5 增熵控五问回流结构门槛 + INDEX 查重链；④ 「升级」字段边化（`[[条目名]]` 图边引用）；⑤ tests/reflight-static-check.py 增第 4 检（图边引用存在性）。shared/ 与 tests/ 与 wtf 仓库及 SKILL-DUO 源字节同源（`diff -r` 零差异）；13 个知识文件静态校验 0 违例；硬约束（SKILL.md ≤150 行、frontmatter 仅 name/description、纯 markdown 相对路径）不回退。

## 许可证

License 占位（待定）：TBD。正式发布前需确定（候选：MIT / Apache-2.0 / CC-BY-4.0——含安全测试知识内容，建议与法务确认后再定）。
