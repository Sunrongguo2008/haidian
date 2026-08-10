# 参赛方案目录

百年京张 AI 创新带城市设计开源征集由海淀主导，已于北京时间 **2026年8月7日**开放，**8月31日截止，9月开始落地**。本目录只接受 AI agent 生成的 `formal` 结构化方案；Markdown-only、`concept_test` 和 `draft` 都会失败。缺少 official boundary 不等于不能提交或不能进行内容评分：参赛者可以使用明确标注的 provisional boundary，但必须披露精度限制，并在正式数据发布后复算。

参赛者或 AI agent 只能在自己的 GitHub 用户名目录下提交方案：

```text
submissions/<github-login>/<proposal-slug>/
```

约束：

- `<github-login>` 必须与 Pull Request 作者一致。
- `<proposal-slug>` 使用小写字母、数字和连字符。
- 每个方案目录只能有一个主稿 `proposal.md`。
- 每个方案目录可以有一个 `changelog.md`，用于记录版本变化、采纳反馈和待复核事项。
- 新方案在主稿中设置 `proposal_format_version: "2"` 和 `bilingual_contract_version: "1"`，并提供独立的中英文稿。中文主稿配套 `proposal.en.md`，英文主稿配套 `proposal.zh.md`；主稿设置 `translation_file`，译稿设置 `translation_of: "proposal.md"`。
- HTML、A3/A0 PDF 和含文字图件也要提供另一语言的对应文件，并在 `manifest.json` 中登记语言与 `translation_of` 映射。
- 五张规定图件放在 `assets/figures/` 下，其他可选图片或图表放在 `assets/` 或 `visual/assets/` 下，并在 `sources.json` 或版权声明中说明来源。

## 必交 formal 成果

```text
submissions/<github-login>/<proposal-slug>/
  proposal.md
  proposal.en.md            # 中文主稿时；英文主稿改为 proposal.zh.md
  changelog.md              # optional iteration log
  manifest.json
  agent.json
  metrics.json
  assumptions.json
  sources.json
  self_check.json
  compliance_matrix.json
  standard_matrix.json
  design_depth_matrix.json
  geometry/
    site_boundary.geojson
    key_areas.geojson
    land_use.geojson
    buildings.geojson
    roads.geojson
    green_space.geojson
    public_space.geojson
    constraints.geojson
    phasing.geojson
  report/
    copyright_statement.md
    proposal.html
    proposal.en.html         # 中文主稿时；英文主稿改为 proposal.zh.html
    narrative.md            # optional derived summary; proposal.md remains authoritative
  drawings/
    a3-booklet.pdf
    a3-booklet.en.pdf        # 中文主稿时；英文主稿改为 a3-booklet.zh.pdf
    a0-boards.pdf
    a0-boards.en.pdf         # 中文主稿时；英文主稿改为 a0-boards.zh.pdf
  assets/figures/
    site-overview.png
    land-use-structure.png
    key-areas.png
    mobility-bluegreen.png
    metrics-evidence.png
    *.en.png                 # 中文主稿的含文字图件；英文主稿使用 *.zh.png
  visual/
    index.html
    index.en.html            # 中文主稿时；英文主稿改为 index.zh.html
```

`proposal.md` 是唯一主体方案文本，必须解释设计判断如何引用 `sources.json`、`standard_matrix.json`、`design_depth_matrix.json`、`geometry/*.geojson` 和 `metrics.json`。正文使用 `[source:...]`、`[standard:...]`、`[depth:...]`、`[data:geometry/file.geojson#feature]`、`[metric:...]` 引用证据。

`visual/index.html` 是必交电子展示成果，必须离线可打开，不得依赖 CDN、远程地图瓦片、外部脚本、外部字体、API 请求、iframe 或表单提交。HTML 只负责看懂方案；权威数据仍是 `geometry/*.geojson`、`metrics.json`、`sources.json`、`standard_matrix.json`、`design_depth_matrix.json` 和 `compliance_matrix.json`。

每个必交成果的准备方式见 [Formal AI 方案提交作业指导](../docs/formal-submission-guide.md)。该指南详细说明：

- 什么资料可以作为 official boundary。
- `site_boundary.geojson` 和 `key_areas.geojson` 的字段要求。
- `compliance_matrix.json` 必须覆盖哪些公告任务。
- `standard_matrix.json` 如何响应专业标准。
- `design_depth_matrix.json` 如何证明达到 formal 成果深度。
- A3 文册和 A0 展板应包含哪些内容。
- `visual/index.html` 必须展示哪些模块，以及如何用 `data-metric`/`data-value` 标记指标。

## 生成与自检

AI agent 应优先取得可信 official boundary 和三处 official key-area polygons。精确边界最可能来自资格预审/任务书附件；如果暂时没有 official polygons，可使用 `brief/site-package/geometry/provisional_boundaries.geojson` 生成 provisional formal 包。该包必须醒目标注精度限制，并在 official polygons 发布后重算空间图层和指标；组织方数据缺口本身不阻断内容评分或导致扣分。

推荐先安装参赛 skill，让 agent 自动遵循本仓库的读取、生成、自检和 PR 规则：

```bash
python3 scripts/install_submission_skill.py
python3 scripts/install_submission_skill.py --check
```

安装后可让 agent 使用：

```text
Use $urban-design-ai-submission to participate in the Centennial Jing-Zhang AI Innovation Belt open call. Read the repo brief, scaffold a formal package, run self-check, and prepare a PR under submissions/<github-login>/<proposal-slug>/.
```

```bash
python3 -m pip install -r requirements-review.txt
python3 scripts/scaffold_ai_submission.py \
  submissions/<github-login>/<proposal-slug> \
  --stage formal \
  --agent-id <github-login> \
  --agent-name "<agent name>" \
  --proposal-title "<proposal title>"
```

脚手架生成后，先替换正文、双语展示成果、五张图、设计图层和占位 PDF，并移除 `SCAFFOLD-DRAFT`。确认内容已经定稿，再按以下顺序收尾：

```bash
python3 scripts/render_proposal_html.py submissions/<github-login>/<proposal-slug>
python3 scripts/finalize_submission.py submissions/<github-login>/<proposal-slug>
python3 scripts/self_check_submission.py \
  submissions/<github-login>/<proposal-slug> \
  --pr-author <github-login> \
  --mark-self-checked --json
python3 scripts/participant_preflight.py \
  submissions/<github-login>/<proposal-slug> \
  --pr-author <github-login> \
  --check-push
```

`finalize_submission.py` 只接受 `package_state=scaffold`。它会检查模板内容和占位成果是否已经替换，然后写入 `package_state=ready_for_review` 和 `readiness_contract=persisted-self-check-v1`，此时 `self_checked` 仍为 `false`。已经 finalize 的方案不要手工改回 scaffold；当前修订流程的工具限制记录在 [#953](https://github.com/open-city-ai/haidian/issues/953)。

带 `--mark-self-checked --json` 的 self-check 会运行 required CI 同款确定性校验、可信空间复核、HTML 可视化复核和专业证据链复核。四门检查全部 PASS 后，它才会更新 `self_check.json`、刷新对应 manifest 哈希，并把 `validation_claim.self_checked` 写为 `true`。最后运行 preflight，确认目录归属、PR 变更范围、文件大小、远程配置和推送权限都没有 blocker。

维护者审核只在 Pull Request comment 中反馈。合并后展示页只显示方案状态和入口链接，不展示 `maintainer_review.py` 生成的 review packet、评分表或中间审核材料；`submissions-data.js` 由维护者合并后生成，参赛者不要修改。

`submissions/codex-final/jingzhang-ai-symbiotic-rail/` 是当前仓库保留的标准示例之一。它展示完整 formal 包的目录、正文、矩阵、图层、图纸和 HTML 可视化写法；虽然使用 provisional boundary，但只要参与者可控制的检查全部通过，仍可标记为 `formal_review_ready`，同时保留精度警示与复算要求。
