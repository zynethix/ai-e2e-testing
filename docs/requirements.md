# ai-e2e-testing · 需求文档

> 一句话定位：端到端测试仓库——用户写自然语言测试步骤，Midscene AI 视觉能力执行步骤、并按用户写的验证点验证；被测网页（id-copy-maker、ai-skill-search）保持零测试污染。

## 1. 背景与痛点

传统 UI 自动化（selenium/playwright 脚本）靠元素 id、像素坐标定位元素，页面结构一变，所有脚本都要跟着改。本项目的目标：测试用例由人写（自然语言步骤 + 验证点），由 AI 视觉能力理解并执行，页面改版不用改脚本。

## 2. 需求点

### 2.1 用例形态

- 用例 = 自然语言步骤 + 验证点，用 YAML 书写（Midscene Test 形态），非程序员可写。
- 关键数值验证（文件尺寸、像素、元数据、文件名、网络请求数等）由 JS 代码精确断言；语义验证（"页面显示 X"、"按钮不可点"）由 AI 判定。
- 原生浏览器控件（确认框等）不画在页面里，由代码接住。

### 2.2 执行

- 一键命令跑全部用例（Playwright Test + Midscene 宿主）。
- 本地接入被测网页：id-copy-maker 用 file:// 打开 index.html；ai-skill-search 本地起 dev server。
- 模型用云端 DeepSeek 视觉模型；被测素材只用公开假数据，不用真实敏感数据。

### 2.3 结果判定与证据

- 每个用例有明确状态：passed / failed / skipped；失败区分「执行/脚本错」与「功能不符（断言失败）」。
- 防假阳性三层：关键数值机器断言（确定性）；AI 断言写具体、可证伪；成功用例留证据可抽查。
- 每次运行保存：Playwright trace、每步截图、Midscene 可视化报告（含 AI 每次判定的截图与依据）、HTML report、JSON results、执行记录。

### 2.4 报告

- 运行后出 HTML 报告；Midscene 报告含 AI 定位框与判定依据，供人工二次核查。

## 3. 范围

### 3.1 第一批（跑通 id-copy-maker 核心流程）

- 覆盖 S1 双面复印件主流程、S2 单面、S9 关水印；试点 S7 视情况收编。
- 成功标准：一键跑、出报告、验证点生效、证据链完整。

### 3.2 第二批

- 复制同一套玩法覆盖 ai-skill-search 核心流程。

## 4. 明确不做（v1）

- 不做测试平台 UI。
- 不接 CI（后续可加）。
- 不动被测项目的存量资产（id-copy-maker 的 test/e2e、playwright.config.mjs 等原地保留）。
- 不用真实证件或敏感数据测试。

## 5. 技术选型

- Midscene.js（字节开源、免费）：自然语言步骤 + AI 视觉定位。
- Playwright Test：执行宿主、trace、报告。
- DeepSeek 视觉模型 API：云端、按量付费（单场景约几分钱、全量回归约几元人民币量级）。
- 用例层 YAML + 验证层 JavaScript。

## 6. 目录形态

- 用例按被测项目分子目录（tests/id-copy-maker/、tests/ai-skill-search/）。
- mock 素材随用例存放。
