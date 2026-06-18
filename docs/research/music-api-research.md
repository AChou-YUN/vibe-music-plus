# 音源接入方案调研

> 调研日期：2026-06-18
> 调研方式：mimo search + Web Search

## 结论先行

推荐路径：**网易云开源 API（TH911 增强版）+ Flutter 桌面端**

理由：
- 有多个成功的 Flutter 桌面端项目验证（DreamMusic、bujuan）
- API 功能完整（搜索/播放/歌词/歌单/登录）
- 社区持续维护，Node.js 技术栈成熟
- 合规风险可通过用户自行登录、不缓存版权内容降低

---

## 方案对比

| 方案 | 合规性 | 稳定性 | 个人可用 | 曲库 | 推荐度 |
|------|--------|--------|---------|------|--------|
| QQ音乐 OpenAPI | ✅ | ✅ | ❌ 需企业 | 🇨🇳 丰富 | ⭐ |
| 网易云开放平台 | ✅ | ⚠️ 限制多 | ⚠️ 需审核 | 🇨🇳 丰富 | ⭐⭐ |
| 网易云开源 API | ⚠️ 灰色 | ⚠️ 依赖维护 | ✅ | 🇨🇳 丰富 | ⭐⭐⭐ |
| Spotify API | ✅ | ✅ | ✅ | 🌍 欧美 | ⭐⭐ |
| 本地文件 | ✅ | ✅ | ✅ | 用户自有 | ⭐⭐ |

## 一、官方平台

### QQ音乐 OpenAPI
- **地址：** developer.y.qq.com
- **限制：** "暂不支持个人开发者申请接入"，需联系商务、企业资质
- **能力：** 搜索、播放、歌单、用户资产
- **频控：** 500次/分钟，需验收后提升
- **结论：** 个人开发者不可用

### 网易云音乐开放平台
- **地址：** developer.music.163.com
- **接入方式：** 移动应用/IOT/网页应用/活动接入
- **限制：** 官方 API 对第三方限制严格，CORS 问题常见
- **结论：** 桌面端接入路径不明确

### Spotify Web API
- **地址：** developer.spotify.com
- **接入方式：** OAuth 2.0
- **优势：** 文档最全、API 最稳定、个人开发者友好
- **限制：** 国内不可用（需 VPN），中文曲库有限
- **桌面 API：** localhost:4370 端口，无需认证，可直接控制桌面客户端

## 二、开源项目（仍在维护）

### NeteaseCloudMusicApi（TH911 增强版）⭐ 推荐
- **GitHub：** github.com/TH911/NeteaseCloudMusicApi
- **技术栈：** Node.js
- **状态：** 原版 API 的持续维护分支，社区 PR 维护
- **功能：** 搜索/播放/歌词/歌单/登录/评论/FM 等完整接口
- **部署：** 支持 Docker，可本地运行

### bujuan（Flutter 跨平台播放器）
- **技术栈：** Flutter + on_audio_query + just_audio
- **特点：** 移动端+桌面端，深度整合本地音乐管理与高品质播放
- **参考价值：** Flutter 桌面端的最佳实践

### YesPlayMusic
- **技术栈：** Web 应用，支持 macOS/Windows/Docker
- **特点：** 高颜值，支持每日签到、Touch Bar、PWA、Last.fm
- **参考价值：** UI 设计参考

### Navidrome
- **技术栈：** 自托管服务器，兼容 Subsonic API
- **特点：** 个人音乐库管理，现代化流媒体服务
- **参考价值：** 本地音乐库方案

### lxmusic 音源
- **特点：** 洛雪音乐的开源音源集合，四平台 FLAC 支持
- **参考价值：** 多平台音源聚合思路

## 三、Flutter 桌面端成熟案例

| 项目 | 平台 | 功能 | 技术栈 |
|------|------|------|--------|
| DreamMusic | macOS/Windows/Linux | 登录/播放/下载/歌词/评论/FM | Flutter + Provider + just_audio |
| bujuan | 移动+桌面 | 本地音乐管理 + 在线播放 | Flutter + on_audio_query + just_audio |
| Bili Music | 跨平台 | B站音频播放 | Flutter |

## 四、技术栈选型参考

| 层 | 推荐 | 备选 |
|----|------|------|
| 框架 | Flutter 3.x + Dart 3.x | — |
| 状态管理 | flutter_riverpod | Provider |
| 路由 | go_router | — |
| 音频播放 | just_audio + audio_service | audioplayers |
| 本地音乐 | on_audio_query | — |
| API 后端 | NeteaseCloudMusicApi（Node.js） | — |
| 本地存储 | Hive | SharedPreferences |

## 五、合规风险与应对

| 风险 | 应对 |
|------|------|
| 版权问题 | 用户自行登录，不缓存版权内容，仅供个人学习 |
| API 被封 | 使用开源维护版本，社区跟进修复 |
| 账号风控 | 不做批量请求，模拟正常用户行为 |
| 逆向争议 | 代码开源，不商业化，注明学习用途 |
