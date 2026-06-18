# AI 辅助开发提示词

> 用于 Cursor / Codex / ChatGPT 辅助 Vibe Music v2 开发

## 提示词 1：升级后端

```
我在升级一个 Flutter 音乐播放器项目的后端。

当前状态：
- backend/package.json 依赖的是 NeteaseCloudMusicApi ^4.32.0（原版，已停止维护）
- backend/server.js 使用 serveNcmApi() 启动
- Node.js 需要升级到 18+

目标：
- 切换到 @neteasecloudmusicapienhanced/api（增强版，社区维护）
- 保持 localhost:3000 端口不变
- 保持所有接口路径不变（Flutter 前端不用改）

请帮我：
1. 修改 backend/package.json，替换依赖
2. 修改 backend/server.js，适配增强版的启动方式
3. 确保接口路径兼容（/cloudsearch, /song/url, /lyric, /login/qr/* 等）

注意：增强版的 GitHub 地址是 https://github.com/NeteaseCloudMusicApiEnhanced/api-enhanced
NPM 包名是 @neteasecloudmusicapienhanced/api
```

## 提示词 2：验证接口兼容性

```
我的 Flutter 音乐播放器前端调用了以下网易云 API 接口（全部是 GET localhost:3000）：

- /login/qr/key — 获取扫码 key
- /login/qr/create — 创建二维码
- /login/qr/check — 检查扫码状态
- /login/cellphone — 手机号登录
- /cloudsearch?keywords=xxx&type=1 — 搜索歌曲
- /song/url?id=xxx&br=320000 — 获取播放地址
- /lyric?id=xxx — 获取歌词
- /user/account?cookie=xxx — 获取用户信息
- /likelist?uid=xxx&cookie=xxx — 喜欢列表
- /like?id=xxx&like=true&cookie=xxx — 收藏歌曲
- /user/playlist?uid=xxx&cookie=xxx — 用户歌单
- /playlist/track/all?id=xxx&cookie=xxx — 歌单歌曲
- /song/detail?ids=xxx&cookie=xxx — 歌曲详情

我现在要从原版 NeteaseCloudMusicApi 切换到增强版 @neteasecloudmusicapienhanced/api。

请帮我检查：这些接口在增强版中是否全部兼容？如果不兼容，需要怎么修改 Flutter 前端代码？
```

## 提示词 3：Flutter 端 API 服务重构（如果需要改）

```
这是我的 netease_service.dart，它通过 HTTP GET 调用 localhost:3000 的网易云 API。

[paste netease_service.dart 内容]

现在后端从 NeteaseCloudMusicApi 原版切换到了增强版。请帮我：

1. 检查所有 API 调用是否兼容
2. 如果有接口路径或参数变化，修改对应代码
3. 增强版支持"歌曲解灰"（自动从其他源获取播放地址），请在 getPlayUrl 中添加解灰逻辑
4. 保持现有的 cookie 管理、Hive 存储、错误处理逻辑不变
```

## 提示词 4：Vibe Mode 概念设计

```
我在做一个 Flutter 桌面端音乐播放器（Windows），核心差异化功能叫 "Vibe Mode"。

关键词：新颖、高级、氛围

请帮我设计 Vibe Mode 的概念方案：
1. 全屏沉浸式音乐体验应该包含哪些视觉元素？
2. 动画和过渡效果怎么设计？（参考 Apple Music、Spotify Canvas、网易云黑胶）
3. 如何用 Flutter 实现动态背景（粒子效果、流体动画、音频可视化）？
4. UI 布局建议（极简控件、突出专辑封面和歌词）
5. 技术实现方案（flutter_animate、rive、lottie 等）

要求：不要生成完整代码，只给出设计思路和技术方案。
```

## 提示词 5：音频可视化实现

```
在 Flutter Windows 桌面端实现音频可视化。

需求：
1. 实时获取音频频谱数据
2. 根据音乐节奏生成动态视觉效果（波形、粒子、色彩变化）
3. 性能要好，不能卡顿

请分析：
1. Flutter 如何获取音频频谱数据？（just_audio? audioplayers? fft 库?）
2. 用什么渲染方案？（CustomPainter? Canvas? Rive? shader?）
3. 推荐的视觉效果类型和实现方式
4. 性能优化建议

只给方案，不写完整代码。
```
