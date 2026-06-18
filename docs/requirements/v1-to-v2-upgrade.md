# Vibe Music v1 → v2 升级指南

> 更新日期：2026-06-18
> 核心变更：NeteaseCloudMusicApi 原版 → 增强版

## 升级目标

将 v1 的网易云 API 从已停止维护的原版 v4.32.0 切换到社区持续维护的增强版，解决：
- 原版加密算法已被网易云识别，账号被风控
- 原版 GitHub 仓库已清空，无法获取更新
- 缺少歌曲解灰功能

## 影响范围

| 层 | 需要改？ | 改动量 |
|----|---------|--------|
| Node.js 后端 | ✅ 需要 | 小（换包名 + 启动方式） |
| Flutter 前端 | ❌ 不需要 | 零 |
| Dart 代码 | ❌ 不需要 | 零 |

**结论：** Flutter 前端通过 HTTP GET 调用 localhost:3000 的 REST 接口，增强版接口路径和返回格式与原版完全兼容，前端零改动。

---

## 步骤一：升级 Node.js 后端

### 1.1 确认 Node.js 版本

```bash
node -v
# 需要 >= 18，如果不够去 https://nodejs.org 下载 LTS 版本
```

### 1.2 修改 backend/package.json

```json
{
  "name": "backend",
  "version": "2.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "@neteasecloudmusicapienhanced/api": "latest"
  }
}
```

**变化：**
- `NeteaseCloudMusicApi` → `@neteasecloudmusicapienhanced/api`
- 版本号从 1.0.0 → 2.0.0

### 1.3 修改 backend/server.js

```javascript
// v1 原版
const { serveNcmApi } = require('NeteaseCloudMusicApi')
serveNcmApi({ port: 3000, host: '127.0.0.1', checkVersion: false })

// v2 增强版（具体写法以增强版 README 为准）
// 方式一：作为服务启动
// 直接运行: node app.js（增强版自带）

// 方式二：作为库引入
const express = require('express')
const { createApiEnhanced } = require('@neteasecloudmusicapienhanced/api')

const PORT = process.env.PORT || 3000
const HOST = process.env.HOST || '127.0.0.1'

async function main() {
  const app = express()
  const api = await createApiEnhanced()
  app.use('/api', api)
  app.listen(PORT, HOST, () => {
    console.log(`[Vibe Music API] running @ http://${HOST}:${PORT}`)
  })
}

main().catch(console.error)
```

> ⚠️ 增强版的具体启动方式以 [官方文档](https://neteasecloudmusicapienhanced.js.org/) 为准，上面是通用参考写法。

### 1.4 重新安装依赖

```bash
cd backend
rm -rf node_modules package-lock.json
npm install
```

### 1.5 启动测试

```bash
node server.js
# 预期输出: [Vibe Music API] running @ http://127.0.0.1:3000
```

---

## 步骤二：验证接口兼容性

启动后端后，用浏览器或 curl 验证关键接口：

```bash
# 搜索
curl "http://127.0.0.1:3000/cloudsearch?keywords=周杰伦&type=1"

# 播放地址
curl "http://127.0.0.1:3000/song/url?id=12345&br=320000"

# 歌词
curl "http://127.0.0.1:3000/lyric?id=12345"

# 扫码登录 key
curl "http://127.0.0.1:3000/login/qr/key"
```

如果都返回 JSON 数据，说明接口兼容，Flutter 前端无需任何改动。

---

## 步骤三：Flutter 前端（无需改动）

v1 的 `netease_service.dart` 调用的所有接口在增强版中完全兼容：

| 接口 | 用途 | 兼容 |
|------|------|------|
| `/login/qr/key` | 获取扫码 key | ✅ |
| `/login/qr/create` | 创建二维码 | ✅ |
| `/login/qr/check` | 检查扫码状态 | ✅ |
| `/login/cellphone` | 手机号登录 | ✅ |
| `/cloudsearch` | 搜索歌曲 | ✅ |
| `/song/url` | 获取播放地址 | ✅ |
| `/lyric` | 获取歌词 | ✅ |
| `/user/account` | 获取用户信息 | ✅ |
| `/likelist` | 喜欢列表 | ✅ |
| `/like` | 收藏歌曲 | ✅ |
| `/user/playlist` | 用户歌单 | ✅ |
| `/playlist/track/all` | 歌单歌曲 | ✅ |
| `/song/detail` | 歌曲详情 | ✅ |

**直接 `flutter run -d windows` 即可，不需要改任何 Dart 代码。**

---

## 步骤四：验证清单

| 检查项 | 方法 | 预期 |
|--------|------|------|
| 后端启动 | `node server.js` | 看到 running @ http://127.0.0.1:3000 |
| 搜索接口 | 浏览器访问 `/cloudsearch?keywords=周杰伦&type=1` | 返回 JSON |
| Flutter 编译 | `flutter run -d windows` | 正常启动 |
| 扫码登录 | 在 app 里扫码 | 登录成功 |
| 播放歌曲 | 搜索并播放一首歌 | 正常播放 |
| 歌词同步 | 播放时查看歌词 | 逐行高亮 |
| 歌单同步 | 查看"我喜欢" | 歌曲列表正确 |

---

## 增强版新增功能

切换到增强版后，可利用的额外能力：

| 功能 | 说明 | 配置 |
|------|------|------|
| 歌曲解灰 | 灰色歌曲自动从其他源获取 | `ENABLE_GENERAL_UNBLOCK=true` |
| 无损音质 | 支持 FLAC 格式 | `ENABLE_FLAC=true` |
| 最高码率 | 自动选择最高码率 | `SELECT_MAX_BR=true` |

---

## 风险与应对

| 风险 | 应对 |
|------|------|
| 增强版接口变化 | 验证步骤三，如有变化调整 netease_service.dart |
| 加密算法再次失效 | 关注增强版 GitHub，社区会跟进修复 |
| Node.js 版本不够 | 下载安装 Node.js 18+ LTS |
