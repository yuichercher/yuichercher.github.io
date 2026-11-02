# 0002 · 使用 GitHub Actions 部署到 GitHub Pages

[English](0002-github-pages-deployment.md) · 中文

- 状态：已采纳
- 日期：2026-11-02

## 背景

当前的 Astro 7 示例博客生成静态文件，需要发布到 `https://yuichercher.github.io/`。源码已经放在 `yuichercher/yuichercher.github.io` 仓库中。变更进入 `main` 后应自动部署，同时保留手动重新构建网站的方式。

仓库已经提交了 npm 锁文件，工程约定也要求固定 Node 版本。本次任务只为现有示例博客建立部署流程；替换模板内容和实现设计属于后续独立变更。

## 决定

使用 GitHub Pages，通过 `.github/workflows/deploy.yml` 中的 GitHub Actions 工作流部署。

- 将 Astro 的 `site` 设为 `https://yuichercher.github.io/`，使规范链接、站点地图和 RSS 链接使用公开地址。保留默认的 `base` 值 `/`：名为 `<username>.github.io` 的仓库部署在域名根路径，而不是仓库名子路径下。
- 保留 Astro 默认的静态输出。Pages 直接提供生成的文件，因此不需要服务端适配器。
- 推送到 `main` 时触发工作流，也支持通过 `workflow_dispatch` 手动触发。在其他分支手动运行时可以构建，但部署任务只发布 `main`。
- 在 `.node-version` 中将 Node 固定为 `24.16.0`，与验证本次变更的本地运行时一致，也满足 Astro 的 Node 版本要求。`actions/setup-node` 读取这个文件，让本地版本管理工具和 CI 可以共用一个版本配置。
- 将构建和部署拆成两个任务。构建任务检出源码、缓存 npm 下载内容，执行 `npm ci` 和 `npm run build`，然后通过 `actions/upload-pages-artifact` 上传 `dist/`。`npm ci` 按锁文件安装依赖树；如果依赖声明与锁文件不一致，就会失败。
- 只有构建成功后才运行 `actions/deploy-pages`，通过 `github-pages` 环境发布构建产物。构建任务只获得 `contents: read` 权限，只有部署任务获得 `pages: write` 和 `id-token: write`。认证由 GitHub 提供，不需要个人访问令牌或额外的仓库密钥。
- 使用 `pages` 并发组和 `cancel-in-progress: false` 串行运行工作流，避免新的推送中断正在进行的部署。

### 首次启用与后续部署

1. 在仓库的 **Settings → Pages → Build and deployment** 中，将 **Source** 设为 **GitHub Actions**。这是仓库设置，提交工作流本身不会自动修改它。
2. 将审核后的工作流和配置推送到 `main`。在 **Actions** 页面查看 **Deploy to GitHub Pages** 运行记录；两个任务都成功后，新网站才会发布。
3. 打开 `https://yuichercher.github.io/`。之后如需手动部署，选择 **Run workflow**，并选择 `main` 分支。

## 备选方案

### Astro 的组合部署 Action

Astro 官方指南推荐 `withastro/action`，它将依赖安装、构建和产物上传合在一起，使用方便，工作流代码也更少。

本次没选的原因：它当前的 v6 实现执行的是 `npm install`。拆成显式步骤后，我们可以使用 `npm ci`，并直接从 `.node-version` 读取固定的 Node 版本，每个阶段也更容易在 Actions 日志中查看。代价是需要多维护几个工作流步骤。

### 从分支发布生成文件

把 `dist/` 提交到 `main` 或独立的 `gh-pages` 分支，也可以让 Pages 提供网站。

没选的原因：每次部署都需要额外提交生成文件或更新分支。上传 Pages 构建产物可以让生成文件留在版本控制之外，也不需要授予工作流推送提交的权限。

## 影响

- 首次启用后，每次推送到 `main` 都会自动发布示例博客。构建失败会阻止部署任务运行，已发布的版本保持不变。
- `dist/` 继续被忽略，不新增应用依赖。Node 和工作流 Action 的版本仍需要主动维护。
- 本地验证使用 Node `24.16.0`、`npm ci` 和 `npm run build`。本地构建成功可以验证输出，但仓库权限和 Pages 设置仍需通过首次成功的 Actions 部署来验证。
- GitHub Pages 能承载当前的静态构建。如果以后需要在请求到来时运行服务端逻辑，就要重新评估托管方案。

## 参考资料

- [Astro：部署到 GitHub Pages](https://docs.astro.build/en/guides/deploy/github/)
- [GitHub：使用自定义工作流部署 GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)
- [Astro 部署 Action v6 的实现](https://github.com/withastro/action/blob/v6/action.yml)
