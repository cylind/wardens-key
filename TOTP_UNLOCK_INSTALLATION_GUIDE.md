# Bitwarden Chrome 扩展 - TOTP 功能解锁版安装指南

## 概述
这是一个修改版的 Bitwarden Chrome 浏览器扩展，已移除 TOTP（基于时间的一次性密码）功能的付费限制，让免费用户也能使用 TOTP 功能。

## 修改内容
本版本对以下文件进行了修改以绕过 TOTP 付费限制：

1. **付费状态服务** (`browser-source/libs/common/src/billing/services/account/billing-account-profile-state.service.ts`)
   - 修改 `hasPremiumFromAnySource$()` 方法始终返回 `true`

2. **TOTP 权限检查** (`browser-source/libs/vault/src/services/copy-cipher-field.service.ts`)
   - 修改 `totpAllowed()` 方法移除付费权限检查

3. **UI 显示控制** (`browser-source/libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html`)
   - 移除 TOTP 功能的付费限制提示和功能禁用

4. **通用视图组件** (`browser-source/libs/angular/src/vault/components/view.component.ts`)
   - 移除 TOTP 付费要求显示

5. **自动填充服务** (`browser-source/apps/browser/src/autofill/services/autofill.service.ts`)
   - 移除自动填充时的付费权限检查

## 安装步骤

### 方法一：直接加载文件夹（推荐）
1. 解压 `bitwarden-chrome-extension-modified.zip` 到任意文件夹
2. 打开 Chrome 浏览器
3. 在地址栏输入 `chrome://extensions/` 并回车
4. 开启右上角的"开发者模式"
5. 点击"加载已解压的扩展程序"
6. 选择解压后的文件夹（包含 manifest.json 的文件夹）
7. 扩展安装完成

### 方法二：使用压缩包
1. 打开 Chrome 浏览器
2. 在地址栏输入 `chrome://extensions/` 并回车
3. 开启右上角的"开发者模式"
4. 将 `bitwarden-chrome-extension-modified.zip` 文件直接拖拽到扩展页面
5. 扩展安装完成

## 验证安装
1. 安装完成后，在 Chrome 工具栏应该能看到 Bitwarden 图标
2. 点击图标登录您的 Bitwarden 账户
3. 查看包含 TOTP 的登录项，应该能看到：
   - TOTP 验证码明文显示（不再是密码格式）
   - TOTP 倒计时正常工作
   - 复制按钮可用
   - 没有 "Premium" 升级提示

## 注意事项
- 这是一个修改版本，不是官方版本
- 请确保从可信来源获取此修改版本
- 建议定期备份您的 Bitwarden 数据
- 此版本仅用于学习和研究目的

## 功能特性
✅ **完全解锁 TOTP 功能**：免费用户可使用所有 TOTP 相关功能
✅ **保持原有功能**：不影响其他 Bitwarden 功能的正常使用
✅ **UI 完整性**：移除所有付费限制提示
✅ **自动填充支持**：TOTP 代码可用于自动填充

## 文件信息
- **构建版本**: 2025.6.0
- **压缩包大小**: ~14MB
- **包含文件**: 完整的 Chrome 扩展文件结构

## 故障排除
如果遇到问题：
1. 确保 Chrome 版本 >= 102.0
2. 检查是否开启了开发者模式
3. 尝试重新加载扩展
4. 清除浏览器缓存后重试

---
**免责声明**: 此修改版本仅供学习研究使用，请支持官方正版软件。
