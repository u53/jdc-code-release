# JDC Code Release

这个公开仓库只负责构建和发布 JDC Code 桌面客户端，不存放私有产品源码。

Actions 会通过只读 Deploy Key 从私有仓库 `u53/jdc-code` 检出指定提交，分别在 GitHub 托管的 macOS 和 Windows runner 上构建安装包。macOS 安装包使用 Developer ID Application 证书签名；当前不执行 Apple notarization。构建产物可作为短期 Actions Artifact 下载，也可上传到 JDC Cloud API。

## 手动发布

在 **Actions → Desktop Client Release → Run workflow** 中填写：

- `source_ref`：私有源码分支、标签或完整 commit SHA，默认 `main`。
- `platform`：`all`、`mac` 或 `win`。
- `deploy`：是否在构建成功后上传到生产发布接口。部署时必须选择 `all`。

工作流会先把 `source_ref` 固定为不可变的 commit SHA，两个平台构建同一份源码，避免构建期间分支移动。

## Actions 配置

仓库变量：

- `JDC_CLOUD_API_URL`：生产 Cloud API 根地址，当前为 `https://api.jdccode.com`。

仓库 Secrets：

- `JDC_SOURCE_REPO_SSH_KEY`：只读检出私有源码的 Deploy Key，已配置。
- `CSC_LINK`：Developer ID `.p12` 的 Base64 内容。
- `CSC_KEY_PASSWORD`：`.p12` 密码。
- `APPLE_TEAM_ID`：Apple Developer Team ID。
- `JDC_RELEASE_UPLOAD_TOKEN`：与生产 Cloud API 相同的上传令牌。

GitHub 不允许读取或跨仓库复制已有 Secret 的原值，因此签名证书和上传令牌必须在本仓库重新设置。不要把证书、密码、私有源码 Token 或发布令牌提交到 Git。

