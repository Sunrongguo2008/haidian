# 贡献指南

感谢你愿意帮这个项目补资料、修工具或改进公开网站。仓库接收两类 Pull Request，先确认自己的改动属于哪一类，可以省下不少返工。

## 选择贡献路径

城市设计投稿请从 [`urban-design-ai-submission`](skills/urban-design-ai-submission/) 开始，完整要求见[正式投稿指南](docs/formal-submission-guide.md)。投稿 PR 只能修改 `submissions/<github-login>/<proposal-slug>/` 下的文件。

代码、测试、文档、公开网站和工具修复属于仓库改进，请按本文继续。不要把仓库改进与城市设计投稿放在同一个 PR 中，两个流程的校验范围不同。

## 开始之前

先搜索[现有 Issues](https://github.com/open-city-ai/haidian/issues) 和[现有 Pull Requests](https://github.com/open-city-ai/haidian/pulls)。功能调整、规则变化或影响多个模块的修复，建议先开 Issue 说明现状、复现方式和预期结果。错字、失效链接等小修可以直接发 PR。

仓库改进适合放在独立的完整 checkout 中。`bootstrap_participant_workspace.py` 创建的轻量工作区主要服务投稿，会省略测试和部分网站资源。首次准备开发环境时可以运行：

```bash
git clone --filter=blob:none https://github.com/<github-login>/haidian.git
cd haidian
git remote add upstream https://github.com/open-city-ai/haidian.git
git fetch upstream main
git switch -c <type>/<short-topic> upstream/main
```

已经配置好 fork 的贡献者从 `git fetch upstream main` 开始即可。动手前查看 `git status --short`，不要覆盖工作区里尚未处理的改动。

## 控制改动范围

一个 PR 只解决一个清楚的问题。沿用当前目录结构和已有实现方式，避免顺手整理无关文件。

以下边界需要特别留意：

* `submissions/<github-login>/` 只保存该账号的方案包，仓库改进 PR 不应包含任何 `submissions/` 文件。
* `gallery-publication.json`、`submissions-data.js` 和 `submissions/README.md` 由维护者管理。
* `.maintainer-review/` 与 `docs/reviews/` 只存放本地评审材料，不能提交。
* 共享来源登记由维护者复核。新增来源请按 [`docs/data-workflow.md`](docs/data-workflow.md) 的格式先开 Issue。
* 仓库文件由 `CODEOWNERS` 统一维护，`.github/`、`brief/`、`schema/`、`scripts/`、`docs/` 和共享数据还列有明确的目录负责人。普通贡献者可以提出修改，PR 中应写清依据、兼容影响和验证结果。
* 不要提交个人信息、非公开规划材料、凭据或未获授权的资产。

## 做与改动相称的检查

所有 PR 至少检查 diff 和空白错误：

```bash
git status --short
git diff --check
```

再按改动内容补充针对性验证：

* Python 工具或校验规则：安装 `requirements-review.txt`，运行对应测试。可用 `python3 -m unittest discover -s tests -p "test_<area>.py"` 选择测试文件。
* 公开资料索引：运行 `python3 scripts/validate_sources.py --json`。
* 仓库级文档、工作流或公开页面：运行 `python3 scripts/prelaunch_check.py --json`。
* 静态页面：通过本地 HTTP 服务打开受影响页面，同时检查窄屏和桌面宽度、控制台报错、资源路径与键盘操作。
* Markdown：逐个确认新增的相对链接和示例命令有效。

提交后用 `git diff --name-only upstream/main...HEAD` 再看一次 PR 的完整文件范围。没有运行某项相关检查时，请在 PR 中说明原因。

## 发起 Pull Request

默认 PR 模板面向城市设计投稿。仓库改进可以删去不适用的投稿清单，换成简短、可复核的说明：

* 现有问题与修改内容；
* 关联 Issue，例如 `Closes #123`；
* 实际运行的检查及结果；
* 已知限制、兼容影响或需要维护者确认的事项。

普通代码、文档和测试 PR 会收到 `non-submission code/docs/test PR` 的 CI 提示，表示投稿包校验不适用。后续仍需等待相关检查和 Code Owner 审查。若维护者提出修改意见，请在同一分支继续提交，并在更新后重新运行相关检查。
