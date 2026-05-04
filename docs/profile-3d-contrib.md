# GitHub Profile 3D Contrib 接入说明

本文档记录当前仓库接入 `yoshi389111/github-profile-3d-contrib` 的方式，以及本地调试和线上部署流程。

## 当前接入结果

- 工作流文件：`.github/workflows/profile-3d.yml`
- README 引用图片：`./profile-3d-contrib/profile-night-rainbow.svg`
- 本地调试脚本：`scripts/profile-3d-local-debug.sh`

只要工作流成功执行，仓库根目录下就会生成或更新 `profile-3d-contrib/` 目录，README 里的 3D 贡献图会自动显示最新产物。

## 线上部署方式

### 1. 工作流行为

当前工作流配置如下：

- 每天北京时间 03:00 自动执行一次
- 支持在 GitHub Actions 页面手动触发
- 执行完成后会把生成的 SVG 提交回当前仓库

工作流核心逻辑：

1. checkout 当前仓库
2. 调用 `yoshi389111/github-profile-3d-contrib@v0.9.2`
3. 生成 `profile-3d-contrib/*.svg`
4. 自动提交并 push

### 2. 首次部署步骤

1. 把本次改动推到 `vincentchyu/vincentchyu`
2. 打开 GitHub 仓库页面
3. 进入 `Actions`
4. 选择 `GitHub-Profile-3D-Contrib`
5. 点击 `Run workflow`
6. 等待任务完成
7. 刷新仓库主页，确认 README 中的 3D 图已经正常显示

### 3. 私有贡献可见性

如果你希望贡献图包含私有仓库贡献，需要检查两个层面：

1. GitHub 个人设置里开启 `Include private contributions on my profile`
2. 如果默认 `secrets.GITHUB_TOKEN` 不能满足你的统计诉求，再额外创建 Personal Access Token，并替换工作流里的 `GITHUB_TOKEN`

默认情况下，公开仓库贡献使用 `secrets.GITHUB_TOKEN` 就够了。

## 本地调试方式

这个 Action 官方支持本地命令行运行。当前仓库额外提供了一个包装脚本，方便你直接在本仓库里调试并拿到最终 SVG。

### 方案 A：直接使用本仓库脚本

先准备一个 GitHub Personal Access Token：

```bash
export GITHUB_TOKEN=ghp_your_token_here
```

执行：

```bash
./scripts/profile-3d-local-debug.sh vincentchyu
```

如果你要生成某一年的历史贡献图：

```bash
./scripts/profile-3d-local-debug.sh vincentchyu 2025
```

执行后会发生这些事：

1. 临时拉取 upstream 仓库
2. 安装依赖
3. 调用官方本地命令生成 SVG
4. 把生成结果同步到当前仓库的 `profile-3d-contrib/`

生成成功后，你可以直接检查：

```bash
ls profile-3d-contrib
```

### 方案 B：手动按官方方式调试

如果你想完全按官方文档调试，也可以手动执行：

```bash
git clone https://github.com/yoshi389111/github-profile-3d-contrib.git
cd github-profile-3d-contrib
npm ci
export GITHUB_TOKEN=ghp_your_token_here
node . vincentchyu
```

官方文档里也给了等价方式：

```bash
node_modules/.bin/ts-node src/index.ts vincentchyu
```

或者：

```bash
npm run build
node . vincentchyu
```

## 建议的验证清单

每次调整后，建议按这个顺序验证：

1. 本地执行 `./scripts/profile-3d-local-debug.sh vincentchyu`
2. 确认 `profile-3d-contrib/profile-night-rainbow.svg` 已生成
3. 在本地预览 README，确认图片路径没有写错
4. push 到 GitHub 后手动触发一次 Actions
5. 检查 Actions 日志里是否出现提交成功
6. 回到个人主页确认最终展示效果

## 常见问题

### 1. README 不显示图片

优先检查：

- `profile-3d-contrib/profile-night-rainbow.svg` 是否真实存在
- README 路径是否仍然是 `./profile-3d-contrib/profile-night-rainbow.svg`
- Actions 是否成功把产物提交回仓库

### 2. 本地脚本报 `GITHUB_TOKEN is required`

说明你还没有导出环境变量，先执行：

```bash
export GITHUB_TOKEN=ghp_your_token_here
```

### 3. 想改成其他主题

当前 README 用的是：

```md
<img src="./profile-3d-contrib/profile-night-rainbow.svg" />
```

可替换成这些常见产物之一：

- `./profile-3d-contrib/profile-green.svg`
- `./profile-3d-contrib/profile-green-animate.svg`
- `./profile-3d-contrib/profile-season.svg`
- `./profile-3d-contrib/profile-night-view.svg`
- `./profile-3d-contrib/profile-night-green.svg`
- `./profile-3d-contrib/profile-night-rainbow.svg`
- `./profile-3d-contrib/profile-gitblock.svg`

## 参考文档

- 官方中文文档：[README.zh-CN.md](https://github.com/yoshi389111/github-profile-3d-contrib/blob/main/docs/README.zh-CN.md)
- 官方仓库主页：[github-profile-3d-contrib](https://github.com/yoshi389111/github-profile-3d-contrib)
