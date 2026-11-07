# ai-e2e-testing · 执行计划

> 执行约定：一次对话完成一个 task；每次新会话先读本文件，找到「当前任务」，再做那一个 task。
> 需求依据：docs/requirements.md（唯一开工依据）。本文件只存执行计划与任务页；需求裁决的最终落点回写 docs/requirements.md。
> 红线（AGENTS.md）：安装依赖、删除/覆盖文件、联网部署、花钱动作先请示；完成后提醒 Git 存档；不动被测项目存量资产。

**当前任务**：Task 1｜需求收口与场景提取——Q1–Q5 已裁决（见「需求裁决」节）；场景提取未开始。

## 结业目标（第一批）

一键命令跑通 id-copy-maker 第一批用例，证据链完整可抽查。

机器检查（全部通过才算结业）：
1. `npm run test:e2e` 退出码 0；
2. 产物 JSON results 中 S1、S2、S9 全部为 passed；
3. 产物目录同时存在：Playwright trace、每步截图、Midscene 可视化报告、Playwright HTML 报告、JSON results。

（产物目录与具体文件名在 Task 5 钉死；第二批 ai-skill-search 在结业后另立目标。）

## 需求裁决（2026-11-07 用户拍板，按倾向执行）

| # | 裁决 | 执行含义 |
|---|---|---|
| Q1 | 运行形态：Playwright Test 宿主 + @midscene/web 加载 YAML | 用例 = YAML 场景文件；JS 精确断言写在 Playwright spec 中；不用 Midscene 独立 CLI |
| Q2 | DeepSeek 模型名 deepseek-flash；key 走 .env 不进 git | .env.example 进 git、真实 key 本机 .env；未设硬预算上限（需求文档量级：单场景几分钱、全量回归几元） |
| Q3 | 被测项目引用：相对路径 ../id-copy-maker/index.html | README 声明目录布局前提：被测项目与本仓库为同工作区兄弟目录 |
| Q4 | S7 暂不纳入第一批 | S1/S2/S9 场景与验证点从 id-copy-maker 的 docs 提取，提取表先给用户过目再写用例 |
| Q5 | 产物落盘 artifacts/ + gitignore + 按时间戳留存 | 具体文件名 Task 5 钉死；提供清空命令 |

已核实（2026-11-07 在线查证）：DeepSeek 官方 API 支持视觉输入，OpenAI 兼容格式，base_url=https://api.deepseek.com，规范模型名 deepseek-flash；deepseek-v4-pro 纯文本不可用。Midscene 官方支持 DeepSeek（MIDSCENE_MODEL_FAMILY=deepseek）。

## Task 1｜需求收口与场景提取

- 目标：裁决 Q1–Q5；产出 S1/S2/S9 操作步骤 + 验证点提取表。
- 动作：只读 id-copy-maker 的 docs（必要时 index.html 对照），提取场景；问题按上表倾向请用户拍板。
- 交付：场景提取表（每条验证点能指出页面落点）+ 裁决记录。
- 验证：提取表与页面一一对应，无凭空步骤；5 问各有裁决。
- 前置：无。

## Task 2｜脚手架与依赖安装

- 目标：项目可列出测试、配置就绪。
- 动作：新建 package.json（@midscene/web、@playwright/test、scripts.test:e2e）、playwright.config.mjs、.gitignore、.env.example；README 补运行说明。npm install 执行前先请示。
- 交付：5 个新文件。
- 验证：npx playwright --version 正常；node --check playwright.config.mjs 通过；npm run test:e2e -- --list 列出用例且零模型调用（零花钱）。
- 前置：Task 1（运行形态定案）。

## Task 3｜S1 最小通路

- 目标：1 条 S1 主路径端到端跑通四关：file:// 打开 index.html → DeepSeek 连通 → YAML 步骤执行 → 报告产出。
- 动作：新建 tests/id-copy-maker/s1-duplex.yaml（+ 最小 spec）；配 .env 后跑单条。
- 交付：1 条通过的用例。
- 验证：退出码 0；产物出现 trace、截图、Midscene 报告；报告内含 AI 每次判定的截图与依据。
- 前置：Task 2；DeepSeek key 到位。

## Task 4｜补齐第一批用例

- 目标：S1 全路径、S2、S9 用例 + JS 精确断言 + 防假阳性写法（具体、可证伪）。
- 动作：按 Task 1 提取表写全 YAML 与断言；mock 素材随用例存放。
- 交付：第一批用例全集。
- 验证：一键跑全量，JSON results 统计与预期一致；负向演练——临时改错一个验证点，该用例变红且失败类型标注「断言失败」而非「执行错」。
- 前置：Task 3。

## Task 5｜证据链与一键命令收尾

- 目标：产物固定落盘、一键命令定型、README 操作手册。
- 动作：钉死产物目录结构与文件名；按时间戳留存 + 清空命令；README 写全运行说明。
- 交付：可交付的第一批。
- 验证：对照成功标准四句逐项打勾（一键跑、出报告、验证点生效、证据链完整）；抽查 1 个成功用例证据与人工判断一致；git status 确认产物全部被忽略。
- 前置：Task 4。

## Task 6｜结业验收与存档

- 目标：结业目标机器检查全过，交付报告，提醒 Git 存档。
- 动作：跑结业目标 3 项检查；产出交付报告；按守则提醒用户 Git 存档。
- 交付：交付报告 + 存档。
- 验证：3 项机器检查全部通过。
- 前置：Task 5。

## 第二批（结业后另立）

ai-skill-search 核心流程：复制同一套玩法，dev server 由脚本自动起停；新目标、新任务页。
