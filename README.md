# Harmony Next CI/CD Pipeline

本仓库提供了一个 **CI/CD pipeline**，用于构建、测试、签名和部署 Harmony Next 应用程序。它自动化了管理构建流程、处理依赖项和确保 HarmonyOS 应用程序顺利部署的工作流程。

当前工作流默认使用公开的 **HarmonyOS 6.1 SDK** 下载源，并将工程的编译目标升级到 **6.1.1(24)**，不再依赖外部预构建 Docker 镜像。

## 特性

- **自动构建**：每次提交到仓库时，自动构建您的 HarmonyOS 应用程序。
- **依赖项管理**：使用 `ohpm` 安装和管理依赖项。
- **签名**：使用预定义的证书和密钥库自动签名 `.hap` 文件。
- **GitHub 发布集成**：将签名后的 `.hap` 文件上传到 GitHub Releases 进行分发。
- **SDK 自举**：GitHub Actions 在运行时直接下载并配置 HarmonyOS SDK，无需维护单独容器镜像。

## 目录

- [特性](#特性)
- [安装](#安装)
- [配置](#配置)
- [使用](#使用)
- [许可证](#许可证)

## 安装

要使用此 pipeline，您需要将其配置到您的 GitHub 仓库中，并安装必要的工具。

### 前提条件

- **GitHub Secrets**：需要配置 GitHub secrets（如 keystore 密码等敏感信息）。

### 设置

1. 将此仓库克隆到您的 GitHub 账户中。
2. 配置必要的 GitHub Secrets（如 `KEY_PASSWORD`、`KEYSTORE_PASSWORD` 等）。
3. 确保您的仓库包含构建应用程序所需的 HarmonyOS 源代码。

## 配置

本仓库使用 GitHub Actions 管理 CI/CD pipeline。主要配置文件在 `.github/workflows/harmonyos-ci.yml` 中。此工作流自动化了以下任务：

1. **Checkout**：从仓库获取最新版本的代码。
2. **设置 JDK**：配置 Java 17。
3. **安装 HarmonyOS SDK**：工作流会下载 `6.1-Release` SDK 并自动注入 `toolchains` 路径。
4. **配置仓库源**：配置 npm 与 ohpm 仓库地址，确保 `hvigor` 与依赖可以正常解析。
5. **安装依赖项**：使用 `ohpm install --all` 安装工程依赖。
6. **构建应用程序**：使用 SDK 自带的 `hvigorw` 工具编译 HarmonyOS 应用程序。
7. **签名应用程序**：使用提供的证书和密钥库签名 `.hap` 文件。
8. **上传到 GitHub Releases**：将签名后的 `.hap` 上传到 GitHub Releases。


## 使用

配置好 GitHub Actions 工作流后，每次向 `master` 分支推送代码或者发起 pull request 时，都会自动运行 pipeline。

- **构建**：工作流会自动运行并构建您的 HarmonyOS 应用程序。
- **签名**：构建完成后，工作流会自动签名 `.hap` 文件。
- **部署**：签名后的 `.hap` 文件会自动上传到 GitHub Releases。

### 触发工作流

您可以通过推送代码到 `master` 分支或向 `master` 发起 pull request 来触发 CI/CD pipeline。工作流会自动捕捉到变化并开始构建流程。

### 调整 SDK 版本

如果后续需要继续升级 SDK，只需要修改工作流中的两个环境变量：

- `HARMONYOS_SDK_RELEASE`
- `HARMONYOS_SDK_ARCHIVE`

以及工程根目录 [build-profile.json5](C:\Users\q3499\Desktop\_tmp_harmony_next_pipeline\build-profile.json5) 中的 `compileSdkVersion` / `targetSdkVersion`。

## 许可证

本项目使用 MIT 许可证 - 详情请见 [LICENSE](LICENSE) 文件。

---

### 备注：
根据您的项目需求，可以调整工作流文件中的路径或添加额外的配置。
