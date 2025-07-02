# Warden's Key - TOTP 功能解锁版安装指南

## 概述
Warden's Key 是基于 Bitwarden 浏览器扩展的修改版本，已移除 TOTP（基于时间的一次性密码）功能的付费限制，让免费用户也能使用完整的 TOTP 功能。

## 🎯 主要特性
- ✅ **完全解锁 TOTP 功能**：免费用户可使用所有 TOTP 相关功能
- ✅ **多浏览器支持**：Chrome、Firefox、Edge、Opera
- ✅ **保持原有功能**：不影响其他 Bitwarden 功能的正常使用
- ✅ **UI 完整性**：移除所有付费限制提示
- ✅ **自动填充支持**：TOTP 代码可用于自动填充
- ✅ **自动化构建**：GitHub Actions 自动构建和发布

## 修改内容
本版本对以下 5 个关键文件进行了修改以绕过 TOTP 付费限制：

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

5. **自动填充服务** (`browser-source/apps/browser/src/autofill/services/autofill.service.ts`)
   - 移除自动填充时的付费权限检查

## 📥 下载安装

### 获取扩展包
1. 前往 [Releases 页面](../../releases) 下载最新版本
2. 选择对应浏览器的扩展包：
   - `dist-chrome-*.zip` - Chrome 浏览器
   - `dist-firefox-*.zip` - Firefox 浏览器
   - `dist-edge-*.zip` - Edge 浏览器
   - `dist-opera-*.zip` - Opera 浏览器

### Chrome 安装步骤
1. 下载并解压 `dist-chrome-*.zip` 到任意文件夹
2. 打开 Chrome 浏览器
3. 在地址栏输入 `chrome://extensions/` 并回车
4. 开启右上角的"开发者模式"
5. 点击"加载已解压的扩展程序"
6. 选择解压后的文件夹
7. 扩展安装完成

### Firefox 安装步骤
1. 下载 `dist-firefox-*.zip`
2. 打开 Firefox 浏览器
3. 在地址栏输入 `about:debugging` 并回车
4. 点击"此 Firefox"
5. 点击"临时载入附加组件"
6. 选择下载的 zip 文件或解压后的 manifest.json 文件
7. 扩展安装完成

### Edge 安装步骤
1. 下载并解压 `dist-edge-*.zip` 到任意文件夹
2. 打开 Edge 浏览器
3. 在地址栏输入 `edge://extensions/` 并回车
4. 开启左下角的"开发人员模式"
5. 点击"加载解压缩的扩展"
6. 选择解压后的文件夹
7. 扩展安装完成

## ✅ 验证安装
1. 安装完成后，在浏览器工具栏应该能看到 Bitwarden 图标
2. 点击图标登录您的 Bitwarden 账户
3. 查看包含 TOTP 的登录项，应该能看到：
   - ✅ TOTP 验证码明文显示（不再是密码格式）
   - ✅ TOTP 倒计时正常工作
   - ✅ 复制按钮可用且无禁用状态
   - ✅ 没有 "Premium" 升级提示徽章
   - ✅ 自动填充时包含 TOTP 代码

## 🔧 自动化构建
本项目使用 GitHub Actions 自动构建：
- **统一工作流**：一个工作流完成构建和发布全流程
- **智能触发**：仅在 browser-source 目录变更时触发构建
- **多平台支持**：同时构建 Chrome、Firefox、Edge、Opera 版本
- **自动发布**：推送到 main 分支时自动创建 GitHub Releases
- **源码打包**：同时提供修改后的源码包供审查
- **版本管理**：自动从 manifest.json 读取版本号

## 🛠️ 手动构建（可选）
如果您想自己构建扩展：

### 环境要求
- Node.js >= 18.0.0
- npm >= 8.0.0

### 构建步骤
```bash
# 进入源码目录
cd browser-source

# 安装依赖
npm install

# 构建 Chrome 版本
cd apps/browser
npm run dist:chrome

# 构建其他版本
npm run dist:firefox
npm run dist:edge
npm run dist:opera
```

构建完成后，扩展文件位于 `browser-source/apps/browser/dist/` 目录。

## ⚠️ 注意事项
- 这是一个修改版本，不是 Bitwarden 官方版本
- 请确保从可信来源（本仓库 Releases）获取扩展
- 建议定期备份您的 Bitwarden 数据
- 此版本仅用于学习和研究目的
- 使用修改版本可能违反软件许可协议，请用户自行承担风险

## 🔍 故障排除
如果遇到问题：

### 通用问题
1. 确保浏览器版本满足最低要求
2. 检查是否开启了开发者模式
3. 尝试重新加载扩展
4. 清除浏览器缓存后重试

### 浏览器最低版本要求
- Chrome >= 102.0
- Firefox >= 91.0
- Edge >= 102.0
- Opera >= 88.0

### TOTP 功能问题
1. 确认登录项已配置 TOTP 密钥
2. 检查系统时间是否准确
3. 尝试重新登录 Bitwarden 账户
4. 检查网络连接是否正常

## 📞 技术支持
如果遇到技术问题，请：
1. 查看 [Issues 页面](../../issues) 寻找解决方案
2. 提交新的 Issue 描述问题
3. 提供详细的错误信息和浏览器版本

---
**免责声明**: 本修改版本仅供技术学习和研究使用。请尊重软件版权，支持官方正版软件的开发。使用修改版本可能违反软件许可协议，请用户自行承担相关风险和责任。
