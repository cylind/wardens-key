# Warden's Key

**Warden's Key** 是基于 Bitwarden 浏览器扩展的修改版本，专门解锁 TOTP（基于时间的一次性密码）功能，让免费用户也能使用完整的 TOTP 功能。

## 🎯 主要特性

- ✅ **完全解锁 TOTP 功能**：免费用户可使用所有 TOTP 相关功能
- ✅ **多浏览器支持**：Chrome、Firefox、Edge、Safari
- ✅ **保持原有功能**：不影响其他 Bitwarden 功能的正常使用
- ✅ **UI 完整性**：移除所有付费限制提示
- ✅ **自动填充支持**：TOTP 代码可用于自动填充
- ✅ **自动化构建**：GitHub Actions 自动构建和发布

## 🚀 快速开始

### 下载安装
1. 前往 [Releases 页面](../../releases) 下载最新版本
2. 选择对应浏览器的扩展包（.zip 文件）
3. 解压到任意文件夹
4. 在浏览器中加载解压后的扩展

### 详细安装指南
请参考 [TOTP_UNLOCK_INSTALLATION_GUIDE.md](TOTP_UNLOCK_INSTALLATION_GUIDE.md)

## 🔧 修改内容

本项目对以下 5 个关键文件进行了修改以解锁 TOTP 功能：

1. **付费状态服务** (`browser-source/libs/common/src/billing/services/account/billing-account-profile-state.service.ts`)
   - 修改 `hasPremiumFromAnySource$()` 方法始终返回 `true`

2. **TOTP 权限检查** (`browser-source/libs/vault/src/services/copy-cipher-field.service.ts`)
   - 修改 `totpAllowed()` 方法移除付费权限检查

3. **UI 显示控制** (`browser-source/libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html`)
   - 移除 Premium 徽章、输入框类型条件判断、TOTP 倒计时显示条件和复制按钮禁用条件

4. **通用视图组件** (`browser-source/libs/angular/src/vault/components/view.component.ts`)
   - 强制 `showPremiumRequiredTotp` 为 `false`，简化 `canGenerateTotp` 逻辑

5. **自动填充服务** (`browser-source/apps/browser/src/autofill/services/autofill.service.ts`)
   - 移除自动填充时的付费权限检查条件

详细的修改说明请参考 [TOTP_MODIFICATION_GUIDE.md](TOTP_MODIFICATION_GUIDE.md)。

## 🚀 自动化构建

本项目已配置 GitHub Actions 自动化工作流：

- **统一构建发布流程** - 一个工作流完成构建和发布
- **多浏览器支持** - 同时构建 Chrome、Firefox、Edge、Opera 扩展
- **自动发布** - 推送到 main 分支时自动创建 GitHub Releases
- **源码打包** - 同时提供修改后的源码包供审查
- **智能触发** - 仅在 browser-source 目录变更时触发构建

## 📁 项目结构

```
wardens-key/
├── browser-source/          # Bitwarden 浏览器扩展源码（已修改）
│   ├── apps/browser/        # 浏览器扩展主要代码
│   ├── libs/               # 共享库代码
│   └── ...                 # 其他源码文件
├── .github/workflows/       # GitHub Actions 工作流
│   └── build-and-release.yml  # 统一构建发布工作流
├── README.md               # 项目说明
├── TOTP_MODIFICATION_GUIDE.md      # 详细修改指南
└── TOTP_UNLOCK_INSTALLATION_GUIDE.md  # 安装指南
```
