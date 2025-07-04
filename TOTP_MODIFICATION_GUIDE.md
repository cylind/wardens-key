# Bitwarden TOTP 功能解锁 - 详细修改指南

## 概述
本文档详细记录了为绕过 Bitwarden 浏览器扩展中 TOTP 功能付费限制而进行的所有代码修改。这些修改使免费用户能够完全使用 TOTP（基于时间的一次性密码）功能。

## 修改原理
Bitwarden 的 TOTP 付费限制通过以下机制实现：
**付费状态检查**：通过 `hasPremiumFromAnySource$()` 检查用户是否有高级订阅

我们的绕过策略是直接返回"已付费"状态，从而解锁所有 TOTP 功能。

## 详细修改记录

### 付费状态服务修改
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



### 代码安全性
- ✅ 不涉及网络请求修改
- ✅ 不修改数据加密逻辑
- ✅ 不影响账户安全
- ✅ 仅修改客户端显示逻辑

---
**免责声明**: 本修改指南仅供技术学习和研究使用。请尊重软件版权，支持官方正版软件的开发。使用修改版本可能违反软件许可协议，请用户自行承担相关风险和责任。
