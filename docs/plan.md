# ai-e2e-testing · 执行计划

> 执行约定：一次对话完成一个 task；每次新会话先读本文件，找到「当前任务」，再做那一个 task。
> 需求依据：docs/requirements.md（唯一开工依据）。本文件只存执行计划与任务页；需求裁决的最终落点回写 docs/requirements.md。
> 红线（AGENTS.md）：安装依赖、删除/覆盖文件、联网部署、花钱动作先请示；完成后提醒 Git 存档；不动被测项目存量资产。

**当前任务**：Task 1｜需求收口与场景提取——Q1–Q7 已裁决（见「需求裁决」节）；场景提取未开始。

## 结业目标（第一批）

一键命令跑通 id-copy-maker 第一批用例，证据链完整可抽查。

机器检查（全部通过才算结业）：
1. `npm run test:e2e` 退出码 0；
2. 运行结果文件中 S1、S2、S9 全部为 passed；
3. 产物目录同时存在：Playwright trace、每步截图、Midscene Test 报告、运行结果文件。

（产物目录与具体文件名在 Task 5 钉死；第二批 ai-skill-search 在结业后另立目标。）

## 需求裁决（用户拍板，按倾向执行）

| # | 裁决 | 执行含义 |
|---|---|---|
| Q1 | 运行形态：Midscene Test 宿主（@midscene/test）+ Playwright 作浏览器驱动 | 用例 = YAML 场景文件；JS 精确断言、接确认框、截获下载封装为自定义 Node（defineNode），YAML 中作为一步调用；fileSetup 把 Playwright page 放进 context 供 Node 使用；Midscene Test 报告替代 Playwright HTML report；trace 由 fileSetup 开启 context.tracing |
| Q2 | DeepSeek 模型名 deepseek-flash；key 走 .env 不进 git | .env.example 进 git、真实 key 本机 .env；未设硬预算上限（需求文档量级：单场景几分钱、全量回归几元） |
| Q3 | 被测项目引用：相对路径 ../id-copy-maker/index.html | README 声明目录布局前提：被测项目与本仓库为同工作区兄弟目录 |
| Q4 | S7 暂不纳入第一批 | S1/S2/S9 场景与验证点从 id-copy-maker 的 docs 提取，提取表先给用户过目再写用例 |
| Q5 | 产物落盘 artifacts/ + gitignore + 按时间戳留存 | 用 MIDSCENE_RUN_DIR=./artifacts 统一产物根（report/ 与 test-results/<runId>/ 落在其下）；具体文件名 Task 5 钉死；提供清空命令 |
| Q6 | 版本：@midscene/test、@midscene/web 1.17.7（当前 latest），playwright 1.64.1 | @midscene/test 1.17.7 的 peerDependencies 精确要求 @midscene/web 1.17.7、playwright ^1.45.0；全部精确锁版本，不用 beta |
| Q7 | 失败分类由本仓库自己判，不靠退出码 | 退出码只有 0 / 1，步骤失败、YAML 解析错、生命周期错、清理错都是 1；分类依据是 summary.json 的逐步错误信息，按失败步骤判：失败步骤是 aiAssert，或是抛出带固定标识断言错误的 JS 断言 Node ⇒ 断言失败；其余（AI 定位/操作失败、模型服务错、YAML 与生命周期错、普通 JS 异常）⇒ 执行错；一键命令跑完后由分类脚本读 summary.json 写出「执行记录」（每个用例：状态 + 失败类型 + 失败步骤） |

已核实（2026-11-07 在线查证）：DeepSeek 官方 API 支持视觉输入，OpenAI 兼容格式，base_url=https://api.deepseek.com，规范模型名 deepseek-flash；deepseek-v4-pro 纯文本不可用。Midscene 官方支持 DeepSeek（MIDSCENE_MODEL_FAMILY=deepseek）。

## Task 1｜需求收口与场景提取

- 目标：裁决 Q1–Q7；产出 S1/S2/S9 操作步骤 + 验证点提取表。
- 动作：只读 id-copy-maker 的 docs（必要时 index.html 对照），提取场景；问题按上表倾向请用户拍板。
- 交付：场景提取表（每条验证点能指出页面落点）+ 裁决记录。
- 验证：提取表与页面一一对应，无凭空步骤；Q1–Q7 各有裁决。
- 前置：无。

## Task 2｜脚手架与依赖安装

- 目标：项目可列出测试、配置就绪。
- 动作：新建 package.json（@midscene/test、@midscene/web、playwright，精确版本；scripts.test:e2e 调 midscene-test）、midscene.config.mjs（setup / fileSetup / nodes，files.include 指向 tests/）、.gitignore、.env.example；README 补运行说明。npm install 执行前先请示。
- 交付：4 个新文件 + README 更新。
- 验证：node --check midscene.config.mjs 通过；npx midscene-test nodes 生成 Node 说明书（只加载配置、不执行 Node），且零模型调用（零花钱）；零花钱探针——一份只含自定义 Node 的 YAML（一步抛断言错误、一步抛普通错误、一步正常），读实际 summary.json，确认能区分两类失败并记下字段结构，作为 Q7 分类脚本的依据。
- 前置：Task 1（运行形态定案）。

## Task 3｜S1 最小通路

- 目标：1 条 S1 主路径端到端跑通四关：file:// 打开 index.html → DeepSeek 连通 → YAML 步骤执行 → 报告产出。
- 动作：新建 tests/id-copy-maker/s1-duplex.yaml（+ fileSetup 打开页面、开 trace）；配 .env 后跑单条。
- 交付：1 条通过的用例。
- 验证：退出码 0；产物出现 trace、截图、Midscene Test 报告；报告内含 AI 每次判定的截图与依据。
- 前置：Task 2；DeepSeek key 到位。

## Task 4｜补齐第一批用例

- 目标：S1 全路径、S2、S9 用例 + JS 精确断言 + 防假阳性写法（具体、可证伪）。
- 动作：按 Task 1 提取表写全 YAML 与断言；mock 素材随用例存放。
- 交付：第一批用例全集。
- 验证：一键跑全量，运行结果文件统计与预期一致；负向演练——临时改错一个验证点，该用例变红且失败类型标注「断言失败」而非「执行错」。
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
