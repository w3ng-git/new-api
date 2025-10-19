# Docker 双发布配置指南

本指南将帮助你配置自动构建和发布 Docker 镜像到 DockerHub 和 GitHub Container Registry (GHCR)。

## 📋 前置要求

- GitHub 账号（已有）
- 需要创建：DockerHub 账号

---

## 第一步：创建 DockerHub 账号和 Access Token

### 1.1 注册 DockerHub 账号

1. 访问 [https://hub.docker.com/signup](https://hub.docker.com/signup)
2. 填写信息注册账号（建议用户名简短，例如：`w3ng`）
3. 验证邮箱

### 1.2 创建 Access Token

1. 登录 DockerHub 后，点击右上角头像
2. 选择 **Account Settings**
3. 左侧菜单选择 **Security**
4. 点击 **New Access Token** 按钮
5. 填写信息：
   - **Description**: `GitHub Actions for new-api`
   - **Access permissions**: 选择 **Read, Write, Delete**
6. 点击 **Generate**
7. **重要**：立即复制生成的 token（只显示一次！）

示例 token 格式：`dckr_pat_xxxxxxxxxxxxxxxxxxxx`

---

## 第二步：在 GitHub 仓库添加 Secrets

### 2.1 访问 Secrets 设置页面

1. 打开你的 GitHub 仓库：[https://github.com/w3ng-git/new-api](https://github.com/w3ng-git/new-api)
2. 点击 **Settings** 标签
3. 左侧菜单展开 **Secrets and variables** → 选择 **Actions**

### 2.2 添加 DOCKERHUB_USERNAME

1. 点击 **New repository secret** 按钮
2. 填写：
   - **Name**: `DOCKERHUB_USERNAME`
   - **Secret**: 你的 DockerHub 用户名（例如：`w3ng`）
3. 点击 **Add secret**

### 2.3 添加 DOCKERHUB_TOKEN

1. 再次点击 **New repository secret**
2. 填写：
   - **Name**: `DOCKERHUB_TOKEN`
   - **Secret**: 粘贴刚才复制的 Access Token
3. 点击 **Add secret**

完成后你应该看到两个 secrets：
- ✅ DOCKERHUB_USERNAME
- ✅ DOCKERHUB_TOKEN

---

## 第三步：修改 Workflow 文件

已经帮你准备好了修改后的文件，你需要确认你的 DockerHub 用户名。

**你的 DockerHub 用户名是什么？**

假设是 `w3ng`，那么镜像地址将是：
- DockerHub: `w3ng/new-api`
- GHCR: `ghcr.io/w3ng-git/new-api`

---

## 第四步：测试配置

### 4.1 推送 workflow 更新

```bash
git add .github/workflows/
git commit -m "chore: 配置自己的 DockerHub 账号"
git push
```

### 4.2 手动触发测试

1. 访问：[https://github.com/w3ng-git/new-api/actions](https://github.com/w3ng-git/new-api/actions)
2. 选择 **Publish Docker image (alpha)** workflow
3. 点击 **Run workflow** → 选择 `main` 分支
4. 点击 **Run workflow** 按钮开始构建

### 4.3 查看构建结果

构建大约需要 10-20 分钟，成功后可以在这里查看：

- DockerHub: `https://hub.docker.com/r/<你的用户名>/new-api`
- GHCR: `https://github.com/w3ng-git/new-api/pkgs/container/new-api`

---

## 使用镜像

构建完成后，可以这样拉取镜像：

```bash
# 从 DockerHub 拉取
docker pull w3ng/new-api:alpha

# 从 GHCR 拉取
docker pull ghcr.io/w3ng-git/new-api:alpha

# 运行容器
docker run -d -p 3000:3000 w3ng/new-api:alpha
```

---

## 自动触发说明

配置完成后，以下情况会自动构建和发布镜像：

1. **推送到 alpha 分支**: 自动构建 `alpha` 标签镜像
2. **创建 Git Tag**: 自动构建发布版本镜像
3. **手动触发**: 随时可以在 Actions 页面手动触发

---

## 常见问题

### Q: GHCR 镜像是公开的吗？
A: 默认是私有的，需要在仓库设置中修改。

**如何公开 GHCR 镜像：**
1. 访问：[https://github.com/users/w3ng-git/packages/container/new-api/settings](https://github.com/users/w3ng-git/packages/container/new-api/settings)
2. 滚动到最下方 **Danger Zone**
3. 点击 **Change visibility** → 选择 **Public**

### Q: 构建失败怎么办？
A: 查看 Actions 日志，常见原因：
- Secrets 配置错误
- DockerHub 用户名或 token 错误
- 网络问题

### Q: 如何删除旧的镜像？
A:
- DockerHub: 登录后在镜像页面删除标签
- GHCR: 在 GitHub Packages 页面删除版本

---

## 下一步

配置完成后，建议：

1. ✅ 修改 `docker-compose.yml`，使用你自己的镜像
2. ✅ 更新 README.md，添加你的镜像拉取命令
3. ✅ 测试一次完整的构建流程

需要帮助？随时问我！
