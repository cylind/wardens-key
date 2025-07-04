# Warden's Key

**Warden's Key** 是基于 Bitwarden 浏览器扩展的修改版本，专门解锁 TOTP（基于时间的一次性密码）功能，让免费用户也能使用完整的 TOTP 功能。

## 🎯 主要特性

- ✅ **完全解锁 TOTP 功能**：免费用户可使用所有 TOTP 相关功能
- ✅ **保持原有功能**：不影响其他 Bitwarden 功能的正常使用
- ✅ **自动化构建**：GitHub Actions 自动构建和发布

## 🚀 快速开始

### 下载安装
1. 前往 [Releases 页面](../../releases) 下载最新版本
2. 选择对应浏览器的扩展包（.zip 文件）
3. 解压到任意文件夹
4. 在浏览器中加载解压后的扩展

<details>
<summary>详细安装指南</summary>

#### 获取扩展包
1. 前往 [Releases 页面](../../releases) 下载最新版本
2. 选择对应浏览器的扩展包：
   - `dist-chrome.zip` - Chrome 浏览器
   - `dist-firefox.zip` - Firefox 浏览器
   - `dist-edge.zip` - Edge 浏览器
   - `dist-opera.zip` - Opera 浏览器

#### Chrome 安装步骤
1. 下载并解压 `dist-chrome-*.zip` 到任意文件夹
2. 打开 Chrome 浏览器
3. 在地址栏输入 `chrome://extensions/` 并回车
4. 开启右上角的"开发者模式"
5. 点击"加载已解压的扩展程序"
6. 选择解压后的文件夹
7. 扩展安装完成

#### Firefox 安装步骤
1. 下载 `dist-firefox-*.zip`
2. 打开 Firefox 浏览器
3. 在地址栏输入 `about:debugging` 并回车
4. 点击"此 Firefox"
5. 点击"临时载入附加组件"
6. 选择下载的 zip 文件或解压后的 manifest.json 文件
7. 扩展安装完成

#### Edge 安装步骤
1. 下载并解压 `dist-edge-*.zip` 到任意文件夹
2. 打开 Edge 浏览器
3. 在地址栏输入 `edge://extensions/` 并回车
4. 开启左下角的"开发人员模式"
5. 点击"加载解压缩的扩展"
6. 选择解压后的文件夹
7. 扩展安装完成

</details>


## 🔧 修改内容

本项目对以下关键文件进行了修改以解锁 TOTP 功能：

**付费状态服务** (`browser-source/libs/common/src/billing/services/account/billing-account-profile-state.service.ts`)
   - 修改 `hasPremiumFromAnySource$()` 方法始终返回 `true`

详细的修改说明请参考 [TOTP_MODIFICATION_GUIDE.md](TOTP_MODIFICATION_GUIDE.md)。

---
## ⚖️ 免责声明

**免责声明**: 本项目仅供技术学习和研究使用。请尊重软件版权，支持官方正版软件的开发。使用修改版本可能违反软件许可协议，请用户自行承担相关风险和责任。