# PORTABILITY.md — 跨 Agent 通用性说明（wpt / pentest，v0.3）

> 本仓库是 SKILL-DUO 项目 pentest skill 的独立发行版。硬约束不变：**skill 不得绑定任何单一运行时**。任何支持 SKILL.md 约定（Anthropic Agent Skills 规范 / agentskills.io 事实标准）的 Agent 都应能直接加载。

## 1. 已遵守的通用规范

| 约束项 | 本仓库做法 |
|---|---|
| frontmatter | 仅 `name` + `description` 两个公开通用字段；name ≤64 字符、小写连字符、与目录名一致；description ≤1024 字符（实测 1007 字符，见 §4）。**不使用** `allowed-tools`、`metadata` 等非通用或平台专有字段 |
| 专有语法 | 全库禁用：特定 Agent 的工具调用格式、`{{...}}` 模板变量、平台专有 hook/拦截指令、`<EXTREMELY-IMPORTANT>` 类社区私有标签。所有指令用通用祈使句书写 |
| 引用方式 | references 全部纯 markdown，相对路径 + 正斜杠，**引用只许一层深**（SKILL.md → reference，不嵌套）；>100 行的引用文件头部加 TOC |
| 无人类向文件 | skill 目录（pentest/）内不放 README/CHANGELOG/INSTALLATION（PORTABILITY.md、README.md 与 tests/ 置于 skill 目录之外，不参与加载） |
| 行数经济 | SKILL.md 正文 85 行（≤150），只做索引+触发+回流，技术细节全部外置 |

## 2. 兼容边界（如实声明，不掩盖）

1. **shared/ 已复制入库（形态 B：复制 + 重新同步）**：本仓库为独立发行版，SKILL-DUO 源项目的共享层（`shared/REFLIGHT.md` 与 `shared/tools/`）已**复制**进本仓库，`pentest/SKILL.md` 以 `../shared/...` 相对路径引用。装载形态：
   - **形态 A（推荐，目录树完整）**：克隆整个仓库后以目录树为单位注册/加载，相对路径直接生效；
   - **形态 B（单目录装载的降级）**：将 `pentest/` 单独拷入仅支持单 skill 目录的运行时前，需把 `shared/` 内容并入 `pentest/` 内并修正引用路径，使 skill 变为自包含副本。
   **同步义务**：复制意味着去重失效——本仓库 `shared/` 与姊妹仓库 [wtf](https://github.com/wufufu770/wtf) 的 `shared/` 保持同源，**任一侧对 `shared/` 的修改必须重新同步到另一侧**（`diff -r` 校验零差异），否则共享层漂移。此义务已在两仓库 README 与 `shared/tools/INDEX.md` 维护规则中注明。
2. **references 的 frontmatter**：SKILL.md 规范只约束 SKILL.md 本身；references 头部的 verified/status/source/changelog 是本项目的**知识管理约定**（YAML 子集，多数 markdown 渲染器可解析，不参与触发机制），任何 Agent 忽略它也不影响加载。状态为五态（hypothesis/verifying/verified/refuted/inconclusive），裁决词 PASS/FAIL/INCONCLUSIVE 为纯文本标准词，任何 Agent 均可静态校验，无平台依赖。
3. **软硬边界（scope 四层纵深下的职责声明）**：pentest 的 scope 门槛为四层纵深——L1 结构化 scope 文件（scope-authorization.md §1）与 L2 循环外 PreToolUse hook（scope-hook-spec.md）属**运行时能力**，本仓库只提供可填模板与平台无关实装规范（仅描述应拦截规则与判定语义，不含任何平台专有语法）；运行时未实装 L2 时，L3 提示词检查清单（SKILL.md GATE-0）与 L4 输出路径分级独立生效，SKILL.md 不假设也不假装拥有 hook 能力。hook 的退出码语义以「非零阻断码 = 拦截」通用语义书写（主流 PreToolUse 实现均兼容）。
4. **触发依赖 description**：触发逻辑全部写在 description 字段内，未假设任何运行时的确定性路由能力；可用 tests/trigger-regression.md 的 20 条用例做触发回归（其中 P-01~P-05 正向用例与 §B 全部负例适用于本仓库；C 系用例属姊妹仓库 ctf skill，保留在清单中便于双仓库统一对照）。
5. **REFLIGHT 协议的通用性**：回流协议（shared/REFLIGHT.md）为纯 markdown 约定——状态机五态、裁决词三态、复跑记录为普通 markdown 表格。它不调用任何平台能力，单 Agent 亦可独立执行（自己写条目、自己复跑、自己推进状态）；多 Agent 场景下可作为跨 Agent 的共享知识库纪律。
6. **shared/tools 完整性说明**：`shared/tools/` 保留全部 6 份工具文件（含 forensics-stego.md），pentest 主线仅引用其中 5 份——保留 forensics-stego.md 是为了与 wtf 仓库共享层字节一致（INDEX.md 路由表无死链 + 形态 B 同步可直接 diff -r）。

## 3. 验证方式

- 加载验证：目标 Agent 装载后，用 tests/trigger-regression.md 的适用用例跑触发回归（每条 3 次取多数）。
- 结构验证：name=目录名、description 长度、SKILL.md 行数、引用一层深，四项可用脚本静态检查。
- 回流质量验证：tests/reflight-static-check.py 做静态校验——裁决词正则 `^(PASS|FAIL|INCONCLUSIVE)$` + 状态词枚举 + 复跑表存在性 + 图边引用存在性（v0.4 第 4 检：[[条目名]] 必须命中全库条目标题）。

## 4. 实测指标（v0.4，2026-10-16 更新）

- pentest/SKILL.md：85 行（≤150 达标）；description 1007 字符（≤1024 达标）。
- v0.4 变更面：shared/REFLIGHT.md（死锁条款 + 熵控五问 + 升级字段边化）、shared/tools/INDEX.md（查重链）、pentest/references/scope-hook-spec.md（规则族 2 循环检测）、tests/reflight-static-check.py（第 4 检）——均与 SKILL-DUO 源及 wtf 仓库字节同源（`diff -r` 零差异）。
- 跨 Agent 通用性硬约束逐项复核：frontmatter 双字段、无专有语法、一层深引用、无人类向文件——均未回退。
