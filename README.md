# Warden's Key

**Warden's Key** 是基于 Bitwarden 浏览器扩展的修改版本，专门解锁 TOTP（时间基础一次性密码）功能，让免费用户也能使用完整的 TOTP 功能。

## 🎯 主要特性

- ✅ **完全解锁 TOTP 功能**：免费用户可使用所有 TOTP 相关功能
- ✅ **多浏览器支持**：Chrome、Firefox、Edge、Safari
- ✅ **保持原有功能**：不影响其他 Bitwarden 功能的正常使用
- ✅ **UI 完整性**：移除所有付费限制提示
- ✅ **自动填充支持**：TOTP 代码可用于自动填充
- ✅ **自动化构建**：GitHub Actions 自动构建和发布
- ✅ **持续更新**：自动监控官方更新并适配

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

1. **计费服务修改** - 绕过付费检查
2. **复制服务修改** - 允许复制 TOTP 代码
3. **UI 模板修改** - 移除付费徽章
4. **视图组件修改** - 显示 TOTP 代码
5. **自动填充修改** - 支持 TOTP 自动填充

详细的修改说明请参考 [TOTP_MODIFICATION_GUIDE.md](TOTP_MODIFICATION_GUIDE.md)。