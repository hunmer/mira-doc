# GitHub Pages 部署指南

此仓库已配置为自动部署VitePress文档到GitHub Pages。

## 设置步骤

### 1. 启用GitHub Pages

在你的GitHub仓库中：

1. 进入 **Settings** 选项卡
2. 在左侧菜单中找到 **Pages**
3. 在 **Build and deployment** 部分：
   - **Source**: 选择 "GitHub Actions"
   - 不需要选择分支，因为我们使用GitHub Actions

### 2. 工作流说明

`.github/workflows/deploy.yml` 文件配置了自动部署：

- **触发条件**: 当推送到 `main` 分支时
- **构建工具**: 使用 pnpm 管理依赖
- **部署目标**: GitHub Pages

### 3. 访问文档

部署完成后，你的文档将在以下地址可用：
```
https://hunmer.github.io/mira_typescript/
```

### 4. 本地开发

```bash
# 进入文档目录
cd mira-doc

# 安装依赖
pnpm install

# 启动开发服务器
pnpm run docs:dev

# 构建生产版本
pnpm run docs:build

# 预览生产版本
pnpm run docs:preview
```

### 5. 配置说明

- **base路径**: 已设置为 `/mira_typescript/` 以匹配GitHub Pages的URL结构
- **工作目录**: 工作流配置为在 `mira-doc` 目录中运行
- **缓存**: 使用pnpm缓存以加快构建速度

## 故障排除

### 部署失败
1. 检查GitHub Actions的日志
2. 确保pnpm-lock.yaml文件已提交
3. 验证package.json中的脚本配置

### 页面404错误
1. 确认base路径设置正确
2. 检查文档链接是否使用相对路径

### 样式或资源加载失败
1. 确认所有静态资源使用相对路径
2. 检查VitePress配置中的base设置
