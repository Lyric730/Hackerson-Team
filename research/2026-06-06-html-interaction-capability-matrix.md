# HTML + 抖音互动空间交互能力矩阵

更新时间：2026-06-06

适用范围：只讨论本次黑客松的 `HTML + 抖音互动空间`。不讨论微信小游戏能力边界，不把抖音小游戏 `tt.*` API 默认带入互动空间。

## 0. 一句话结论

我们可以稳定做一个离线 H5 互动作品：触摸、拖拽、长按、滑动、Canvas / DOM 视觉反馈、本地音效、本地状态都能做。

震动、陀螺仪、加速度 / 摇晃：HTML 代码层可以写对应 Web API，但目前没有看到互动空间官方文档承诺这些 API 一定可用；所以它们属于“可尝试调用，但不是互动空间明示能力”。

`tt.vibrateShort`、`tt.onAccelerometerChange`、`tt.startGyroscope` 这类是抖音小程序 / 小游戏 API；互动空间离线 H5 不能默认调用。

## 1. 能力分级

| 交互手段 | HTML 代码能不能写 | 互动空间是否明确承诺 | 当前结论 |
|---|---:|---:|---|
| 点击 / 点按 | 能 | 是，本地规范面向移动端触控体验 | 能作为核心交互 |
| 拖拽 | 能 | 是，本地规范支持移动端交互 | 能作为核心交互 |
| 长按蓄力 | 能 | 是，基于 pointer / touch / timer 实现 | 能作为核心交互 |
| 滑动 / 划线 | 能 | 是，基于 pointer / touch 路径实现 | 能作为核心交互 |
| 多点触控 | 能 | 未单独承诺 | 可写，但不要当平台专项能力 |
| DOM 视觉反馈 | 能 | 是 | 能作为核心反馈 |
| CSS 动画 / 变形 / 屏幕抖动 | 能 | 是 | 能作为核心反馈 |
| Canvas 2D 绘制 / 粒子 / 碰撞 | 能 | 是，本地规范允许 Canvas | 能作为核心反馈 |
| SVG 图形反馈 | 能 | 是，本地规范允许 SVG / 本地资源 | 能作为核心反馈 |
| 本地音效 | 能 | 未禁止；需使用本地文件 | 能做 |
| 本地存档 | 能，`localStorage` | 是，本地规范推荐 | 能做 |
| 震动 | 能，`navigator.vibrate()` | 未看到互动空间明示承诺 | 代码能写；不算平台保证能力 |
| 陀螺仪 / 手机倾斜方向 | 能，`DeviceOrientationEvent` | 未看到互动空间明示承诺 | 代码能写；不算平台保证能力 |
| 加速度 / 摇晃 | 能，`DeviceMotionEvent` | 未看到互动空间明示承诺 | 代码能写；不算平台保证能力 |
| 相机 | HTML 有 `getUserMedia` | 互动空间规范未承诺 | 不按互动空间可用能力处理 |
| 麦克风 | HTML 有 `getUserMedia` | 互动空间规范未承诺 | 不按互动空间可用能力处理 |
| 定位 | HTML 有 Geolocation API | 互动空间规范未承诺 | 不按互动空间可用能力处理 |
| 剪贴板 | HTML 有 Clipboard API | 互动空间规范未承诺 | 不按互动空间可用能力处理 |
| Web Share 系统分享 | HTML 有 Web Share API | 互动空间规范未承诺，平台分享方式受平台控制 | 不按互动空间可用能力处理 |
| 网络请求 | HTML 能写 `fetch` / XHR | 本地规范禁止 | 不能做 |
| WebSocket / EventSource | HTML 能写 | 本地规范禁止 | 不能做 |
| iframe | HTML 能写 | 本地规范禁止 | 不能做 |
| 外部跳转 | HTML 能写 | 本地规范禁止 | 不能做 |
| 浏览器原生 alert / confirm / prompt | HTML 能写 | 本地规范禁止 | 不能做 |
| 抖音小游戏 `tt.*` API | 不是普通 H5 API | 抖音小游戏支持，互动空间未承诺 | 不能默认做 |
| 登录 / 支付 / 广告 / 真实排行榜 | 抖音小游戏/小程序有平台 API | 互动空间离线 H5 未承诺，且本地规范禁网络 | 不能按本次形态做 |

## 2. 震动

结论：HTML 代码能写，互动空间未明示承诺。

可写的代码形态：

```js
if ("vibrate" in navigator) {
  navigator.vibrate(40);
}
```

不能默认写成：

```js
tt.vibrateShort();
```

原因：`tt.vibrateShort` 是抖音小程序 / 小游戏 API；抖音小游戏运行时和互动空间 H5 不是同一套运行环境。互动空间当前交付是 `index.html` 离线 H5。

## 3. 陀螺仪 / 方向

结论：HTML 代码能写，互动空间未明示承诺。

可写的代码形态：

```js
window.addEventListener("deviceorientation", (event) => {
  const { alpha, beta, gamma } = event;
  // Use alpha / beta / gamma to drive UI.
});
```

不能默认写成：

```js
tt.startGyroscope();
```

## 4. 加速度 / 摇晃

结论：HTML 代码能写，互动空间未明示承诺。

可写的代码形态：

```js
window.addEventListener("devicemotion", (event) => {
  const acc = event.accelerationIncludingGravity;
  // Use acc.x / acc.y / acc.z to detect shake.
});
```

不能默认写成：

```js
tt.onAccelerometerChange();
```

## 5. 最后能实现什么

确定能实现：

- 用手点、按、拖、划、长按，让页面元素移动、变形、吸附、爆开、坠落、合成、消除。
- 用 DOM / CSS / Canvas 做强视觉反馈：屏幕抖动、粒子、冲击波、颜色闪烁、缩放、旋转、掉落、碰撞。
- 用本地音频做点击、命中、失败、结算反馈。
- 用 `localStorage` 保存本地最高分、设置、最近结果。
- 生成适合截图的结算页、称号、分数、稀有结果。

代码可以尝试实现，但不能按互动空间明示能力承诺：

- 震动：`navigator.vibrate()`
- 陀螺仪 / 手机倾斜：`DeviceOrientationEvent`
- 加速度 / 摇晃：`DeviceMotionEvent`
- 多点触控：Pointer events 的多个 `pointerId`

当前不能按 `HTML + 互动空间` 实现：

- 直接调用 `tt.*` 小游戏 / 小程序系统 API。
- 真实好友榜、真实平台分享回调、登录态、云存档、远程排行。
- 在线 AI、接口请求、WebSocket 实时通信。
- iframe、外链跳转、外部 CDN 资源。
- 广告、支付、激励视频。

## 6. 团队口径

做玩法设计时，把“确定能实现”的触摸与视觉反馈当主轴。

震动、陀螺仪、摇晃可以写测试代码尝试，但它们不是互动空间公开承诺的底层能力。

如果要一句话对外说明：

> 我们这次能做的是离线 H5 强交互作品；触摸、视觉、音效、本地状态确定能做。震动、陀螺仪、摇晃在 HTML 代码层能尝试，但不是互动空间明示保证能力，不能当作平台必然支持的系统能力。

## 7. 来源

- 本仓库：`HACKATHON_CONTEXT.md`
- 抖音小游戏开发指南：https://developer.open-douyin.com/docs/resource/zh-CN/mini-game/develop/guide/dev-guide/bytedance-mini-game
- 抖音 `tt.vibrateShort` 文档：https://developer.open-douyin.com/docs/resource/zh-CN/mini-app/develop/api/device/shake/tt-vibrate-short/
- MDN Vibration API：https://developer.mozilla.org/en-US/docs/Web/API/Vibration_API
- MDN Device orientation events：https://developer.mozilla.org/en-US/docs/Web/API/Device_orientation_events/Detecting_device_orientation
