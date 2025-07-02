# Bitwarden TOTP 功能解锁 - 详细修改指南

## 概述
本文档详细记录了为绕过 Bitwarden 浏览器扩展中 TOTP 功能付费限制而进行的所有代码修改。这些修改使免费用户能够完全使用 TOTP（基于时间的一次性密码）功能。

## 修改原理
Bitwarden 的 TOTP 付费限制通过以下机制实现：
1. **付费状态检查**：通过 `hasPremiumFromAnySource$()` 检查用户是否有高级订阅
2. **权限验证**：在 TOTP 相关功能中验证用户权限
3. **UI 控制**：根据付费状态控制界面显示和功能可用性

我们的绕过策略是在这些检查点直接返回"已付费"状态，从而解锁所有 TOTP 功能。

## 详细修改记录

### 1. 付费状态服务修改
**文件路径**: `browser-source/libs/common/src/billing/services/account/billing-account-profile-state.service.ts`
**修改行号**: 40-51
**修改目的**: 让系统认为所有用户都有高级权限

#### 修改前代码:
```typescript
hasPremiumFromAnySource$(userId: UserId): Observable<boolean> {
  return this.stateProvider
    .getUser(userId, BILLING_ACCOUNT_PROFILE_KEY_DEFINITION)
    .state$.pipe(
      map(
        (profile) =>
          profile?.hasPremiumFromAnyOrganization === true ||
          profile?.hasPremiumPersonally === true,
      ),
    );
}
```

#### 修改后代码:
```typescript
hasPremiumFromAnySource$(userId: UserId): Observable<boolean> {
  // MODIFIED: Always return true to bypass premium restrictions for TOTP
  return this.stateProvider
    .getUser(userId, BILLING_ACCOUNT_PROFILE_KEY_DEFINITION)
    .state$.pipe(
      map(
        (profile) =>
          // Always return true to enable TOTP for all users
          true,
      ),
    );
}
```

#### 修改原理:
- 原代码检查用户的个人订阅状态和组织订阅状态
- 修改后直接返回 `true`，绕过所有付费检查
- 这是最核心的修改，影响整个应用的付费状态判断

### 2. TOTP 权限检查修改
**文件路径**: `browser-source/libs/vault/src/services/copy-cipher-field.service.ts`
**修改行号**: 153-164
**修改目的**: 简化 TOTP 权限检查逻辑

#### 修改前代码:
```typescript
async totpAllowed(cipher: CipherView): Promise<boolean> {
  const activeAccount = await firstValueFrom(this.accountService.activeAccount$);
  if (!activeAccount?.id) {
    return false;
  }
  return (
    (cipher?.login?.hasTotp ?? false) &&
    (cipher.organizationUseTotp ||
      (await firstValueFrom(
        this.billingAccountProfileStateService.hasPremiumFromAnySource$(activeAccount.id),
      )))
  );
}
```

#### 修改后代码:
```typescript
async totpAllowed(cipher: CipherView): Promise<boolean> {
  const activeAccount = await firstValueFrom(this.accountService.activeAccount$);
  if (!activeAccount?.id) {
    return false;
  }
  // MODIFIED: Simply check if cipher has TOTP, bypass premium restrictions
  return (cipher?.login?.hasTotp ?? false);
}
```

#### 修改原理:
- 原代码需要同时检查密码项是否有 TOTP 和用户是否有权限
- 修改后只检查密码项是否配置了 TOTP，移除权限检查
- 这确保任何有 TOTP 的密码项都可以生成验证码

### 3. UI 显示控制修改
**文件路径**: `browser-source/libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html`
**修改行号**: 122-154
**修改目的**: 移除界面上的付费限制提示和功能禁用

#### 修改前代码:
```html
<bit-form-field *ngIf="cipher.login.totp">
  <bit-label [appTextDrag]="totpCodeCopyObj?.totpCode"
    >{{ "verificationCodeTotp" | i18n }}
    <span
      *ngIf="!(isPremium$ | async)"
      bitBadge
      variant="success"
      class="tw-ml-2 tw-cursor-pointer"
      (click)="getPremium(cipher.organizationId)"
      slot="end"
    >
      {{ "premium" | i18n }}
    </span>
  </bit-label>
  <input
    id="totp"
    readonly
    bitInput
    [type]="!(isPremium$ | async) ? 'password' : 'text'"
    [value]="totpCodeCopyObj?.totpCodeFormatted || '*** ***'"
    aria-readonly="true"
    data-testid="login-totp"
    class="tw-font-mono"
  />
  <div
    *ngIf="isPremium$ | async"
    bitTotpCountdown
    [cipher]="cipher"
    bitSuffix
    (sendCopyCode)="setTotpCopyCode($event)"
  ></div>
  <button
    bitIconButton="bwi-clone"
    bitSuffix
    type="button"
    [appCopyClick]="totpCodeCopyObj?.totpCode"
    [valueLabel]="'verificationCodeTotp' | i18n"
    showToast
    [appA11yTitle]="'copyVerificationCode' | i18n"
    data-testid="copy-totp"
    [disabled]="!(isPremium$ | async)"
    class="disabled:tw-cursor-default"
  ></button>
</bit-form-field>
```

#### 修改后代码:
```html
<bit-form-field *ngIf="cipher.login.totp">
  <bit-label [appTextDrag]="totpCodeCopyObj?.totpCode"
    >{{ "verificationCodeTotp" | i18n }}
    <!-- MODIFIED: Removed premium badge requirement -->
  </bit-label>
  <input
    id="totp"
    readonly
    bitInput
    type="text"
    [value]="totpCodeCopyObj?.totpCodeFormatted || '*** ***'"
    aria-readonly="true"
    data-testid="login-totp"
    class="tw-font-mono"
  />
  <div
    bitTotpCountdown
    [cipher]="cipher"
    bitSuffix
    (sendCopyCode)="setTotpCopyCode($event)"
  ></div>
  <button
    bitIconButton="bwi-clone"
    bitSuffix
    type="button"
    [appCopyClick]="totpCodeCopyObj?.totpCode"
    [valueLabel]="'verificationCodeTotp' | i18n"
    showToast
    [appA11yTitle]="'copyVerificationCode' | i18n"
    data-testid="copy-totp"
    class="disabled:tw-cursor-default"
  ></button>
</bit-form-field>
```

#### 修改原理:
- 移除了 Premium 徽章显示条件 `*ngIf="!(isPremium$ | async)"`
- 将输入框类型从条件判断 `[type]="!(isPremium$ | async) ? 'password' : 'text'"` 改为固定的 `type="text"`
- 移除了 TOTP 倒计时组件的显示条件 `*ngIf="isPremium$ | async"`
- 移除了复制按钮的禁用条件 `[disabled]="!(isPremium$ | async)"`

### 4. 通用视图组件修改
**文件路径**: `libs/angular/src/vault/components/view.component.ts`
**修改行号**: 522-523, 533-536
**修改目的**: 在通用视图中移除 TOTP 付费要求

#### 修改前代码:
```typescript
this.showPremiumRequiredTotp =
  this.cipher.login.totp && !this.canAccessPremium && !this.cipher.organizationUseTotp;

const canGenerateTotp =
  this.cipher.type === CipherType.Login &&
  this.cipher.login.totp &&
  (this.cipher.organizationUseTotp || this.canAccessPremium);
```

#### 修改后代码:
```typescript
// MODIFIED: Never show premium required for TOTP
this.showPremiumRequiredTotp = false;

// MODIFIED: Always allow TOTP generation if cipher has TOTP
const canGenerateTotp =
  this.cipher.type === CipherType.Login &&
  this.cipher.login.totp;
```

#### 修改原理:
- 强制 `showPremiumRequiredTotp` 为 `false`，不显示付费要求提示
- 简化 `canGenerateTotp` 逻辑，只检查密码项类型和是否有 TOTP

### 5. 自动填充服务修改
**文件路径**: `apps/browser/src/autofill/services/autofill.service.ts`
**修改行号**: 486-494
**修改目的**: 在自动填充时移除 TOTP 付费限制

#### 修改前代码:
```typescript
// Skip getting the TOTP code for clipboard in these cases
if (
  options.cipher.type !== CipherType.Login ||
  totp !== null ||
  !options.cipher.login.totp ||
  (!canAccessPremium && !options.cipher.organizationUseTotp)
) {
  return;
}
```

#### 修改后代码:
```typescript
// Skip getting the TOTP code for clipboard in these cases
// MODIFIED: Removed premium restriction for TOTP
if (
  options.cipher.type !== CipherType.Login ||
  totp !== null ||
  !options.cipher.login.totp
) {
  return;
}
```

#### 修改原理:
- 移除了付费权限检查条件 `(!canAccessPremium && !options.cipher.organizationUseTotp)`
- 确保自动填充时也能包含 TOTP 代码

## 完整编译流程

### 环境要求
- Node.js >= 18.0.0
- npm >= 8.0.0
- Git

### 编译步骤

1. **下载源代码**
```bash
wget https://github.com/bitwarden/clients/releases/download/browser-v2025.6.0/browser-source-2025.6.0.zip
unzip browser-source-2025.6.0.zip
rm browser-source-2025.6.0.zip
cd browser-source
```

2. **安装依赖**
```bash
npm install --force
```

3. **构建 Chrome 扩展**
```bash
cd apps/browser
npm run build:prod:chrome
```

4. **构建其他浏览器版本**
```bash
# Firefox
npm run build:prod:firefox

# Edge
npm run build:prod:edge

# Safari
npm run build:prod:safari
```

5. **打包扩展**
```bash
# 创建 ZIP 压缩包
cd build
powershell -Command "Compress-Archive -Path '*' -DestinationPath '../bitwarden-chrome-extension-modified.zip' -Force"

# 或使用 tar
tar -czf ../bitwarden-chrome-extension-modified.tar.gz -C . .
```

### 构建输出
- **Chrome**: `apps/browser/build/` 目录
- **Firefox**: `apps/browser/build/` 目录（使用不同的 manifest）
- **压缩包**: `apps/browser/bitwarden-chrome-extension-modified.zip`

## 版本兼容性说明

### 当前支持版本
- **Bitwarden 版本**: 2025.6.0
- **Chrome 最低版本**: 102.0
- **Firefox 最低版本**: 91.0
- **Edge 最低版本**: 102.0

### 未来版本更新注意事项

#### 可能的变更点
1. **文件路径变更**: 官方可能重构代码结构，导致文件路径改变
2. **函数签名变化**: 付费检查函数的参数或返回值可能改变
3. **新增付费检查点**: 官方可能在新位置添加付费验证
4. **UI 框架升级**: Angular 版本升级可能影响模板语法

#### 更新策略
1. **对比文件差异**: 使用 `git diff` 对比官方更新
2. **搜索关键词**: 在新版本中搜索 `premium`、`hasPremium`、`totp`、`billing` 等关键词
3. **测试验证**: 每次更新后必须测试 TOTP 功能是否正常
4. **备份机制**: 保留可用版本的备份

#### 关键文件监控列表
- `libs/common/src/billing/services/account/billing-account-profile-state.service.ts`
- `libs/vault/src/services/copy-cipher-field.service.ts`
- `libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html`
- `libs/angular/src/vault/components/view.component.ts`
- `apps/browser/src/autofill/services/autofill.service.ts`

## 故障排除

### 常见问题
1. **编译失败**: 检查 Node.js 版本和依赖安装
2. **TOTP 不显示**: 确认所有修改都已正确应用
3. **扩展无法加载**: 检查 manifest.json 文件完整性
4. **功能异常**: 清除浏览器缓存后重试

### 验证修改成功
1. 安装修改后的扩展
2. 登录 Bitwarden 账户
3. 查看包含 TOTP 的登录项
4. 确认以下功能正常：
   - TOTP 代码明文显示
   - 倒计时正常工作
   - 复制按钮可用
   - 无 Premium 升级提示

## 自动化检测脚本

### 检测修改是否生效
创建以下脚本来验证修改是否正确应用：

```bash
#!/bin/bash
# verify_modifications.sh

echo "验证 TOTP 修改是否生效..."

# 检查付费状态服务修改
if grep -q "// Always return true to enable TOTP for all users" libs/common/src/billing/services/account/billing-account-profile-state.service.ts; then
    echo "✅ 付费状态服务修改已应用"
else
    echo "❌ 付费状态服务修改未找到"
fi

# 检查权限检查修改
if grep -q "// MODIFIED: Simply check if cipher has TOTP, bypass premium restrictions" libs/vault/src/services/copy-cipher-field.service.ts; then
    echo "✅ TOTP 权限检查修改已应用"
else
    echo "❌ TOTP 权限检查修改未找到"
fi

# 检查 UI 修改
if grep -q "<!-- MODIFIED: Removed premium badge requirement -->" libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html; then
    echo "✅ UI 显示控制修改已应用"
else
    echo "❌ UI 显示控制修改未找到"
fi

echo "验证完成"
```

### 官方版本更新检测
```bash
#!/bin/bash
# check_updates.sh

echo "检查关键文件是否有更新..."

# 定义关键文件列表
files=(
    "libs/common/src/billing/services/account/billing-account-profile-state.service.ts"
    "libs/vault/src/services/copy-cipher-field.service.ts"
    "libs/vault/src/cipher-view/login-credentials/login-credentials-view.component.html"
    "libs/angular/src/vault/components/view.component.ts"
    "apps/browser/src/autofill/services/autofill.service.ts"
)

for file in "${files[@]}"; do
    if git diff HEAD~1 HEAD --name-only | grep -q "$file"; then
        echo "⚠️  关键文件有更新: $file"
        echo "   需要检查是否需要重新应用修改"
    fi
done

echo "检查完成"
```

## 修改影响范围

### 直接影响
- ✅ TOTP 代码生成和显示
- ✅ TOTP 复制功能
- ✅ TOTP 倒计时显示
- ✅ 自动填充中的 TOTP

### 不受影响的功能
- ✅ 密码管理基本功能
- ✅ 密码生成器
- ✅ 安全报告
- ✅ 数据同步
- ✅ 其他高级功能（如果有组织权限）

### 潜在风险
- ⚠️  官方更新可能覆盖修改
- ⚠️  新版本可能引入新的付费检查点
- ⚠️  修改可能影响其他未知功能

## 技术细节

### 修改的技术原理
1. **状态管理绕过**: 通过修改 Observable 流直接返回付费状态
2. **权限检查简化**: 移除复杂的权限验证逻辑
3. **UI 条件移除**: 删除基于付费状态的条件渲染
4. **服务层修改**: 在服务层面绕过付费限制

### 代码安全性
- ✅ 不涉及网络请求修改
- ✅ 不修改数据加密逻辑
- ✅ 不影响账户安全
- ✅ 仅修改客户端显示逻辑

---
**免责声明**: 本修改指南仅供技术学习和研究使用。请尊重软件版权，支持官方正版软件的开发。使用修改版本可能违反软件许可协议，请用户自行承担相关风险和责任。
