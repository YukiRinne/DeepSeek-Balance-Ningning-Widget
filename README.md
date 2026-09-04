# DeepSeek Balance Ningning Widget（宁宁余额挂件）

DeepSeek Harness（DSH）Web 界面右下角的常驻余额小挂件：宁宁形象 + DeepSeek API 余额 + 今日已用 + 每轮对话消耗统计，每次打开界面自动启用。


## 特性

- 🐋 **常驻自启**：随 DSH Web 界面每次打开自动出现（标准 DSH bundle 插件）
- 💰 **余额**：60 秒自动刷新 + 点击宠物手动刷新；余额变化时数字滚动动画；瞬时网络抖动自动沿用最近余额不报错
- 📒 **今日已用**：两种模式任选
  - **余额记账（推荐，免令牌）**：不需要任何会话令牌，每次观测余额后用余额差值自动记账（跨天自动归零归档）
  - **实时·令牌**：填写平台会话令牌后直接调用平台用量接口，按峰谷定价实时换算
- 🧾 **每轮对话消耗统计**：监听会话事件，每轮对话结束后弹出本轮消耗金额（精确 usage，非估算）
- 🔊 **任务完成提示音**：每轮任务完成后自动播放 `0721.mp3`（「请看我~」），跟随音量设置
- 🖱 **拖拽 + 四边四分之一吸附**（左/右 + 上/下，角落可组合）
- 🔄 左吸附时整体水平镜像翻转（文字同步反向、带动画）
- 🎈 **按压 Q 弹**玩偶效果
- 🍔 **汉堡菜单**：大小滑块（0.6–2.5 倍）、音效切换（小黄鸭 / 音效1）、音量调节、用量模式、峰谷提示文案、气泡开关、每轮消耗开关与自动关闭时间
- 🔊 **音效**：按下/松手音效（可替换包内 mp3，缺失时静默降级）
- 💬 **随机台词**：点击气泡切换随机台词段（加权随机，含峰谷提示 / 今日已用 / gif 动图 / 宁宁定制卖萌台词：`Ciallo～(∠・ω )⌒☆`、`今天也是元气满满的宁宁~`、`欧纳尼……` 等），再点一次关闭；气泡总显示 5 秒自动收起
- 📐 随浏览器窗口自动缩放；文字位置与字号与图片联动

## 目录结构

```
dsh-ningning-widget/
├── package.json        # bundle 声明（dsh.bundle.patch）
├── cordis.patch.yml    # 挂载声明（插入插件行）
├── lib/index.js        # 宿主插件（路由/记账/会话监听）+ 内嵌页面挂件代码
└── assets/             # 素材：宁宁形象图、音效 mp3、rua.gif
```

## 安装

```sh
# 在仓库根目录（package.json 所在目录）本地安装
dsh plugin --profile web add link:.
# 或发布到 npm 后
dsh plugin --profile web add dsh-ningning-widget
```

重启 `dsh web`，浏览器 F5 刷新后右下角即出现挂件。

## 环境依赖

- `DEEPSEEK_API_KEY`（余额接口必需，从 DSH 凭据服务读取）
- `DEEPSEEK_PLATFORM_TOKEN`（可选，仅"实时·令牌"用量模式需要；缺失时自动回落记账模式）

## 验证

```sh
curl http://127.0.0.1:3080/dsh-ningning/image.png    # 200 image/png
curl http://127.0.0.1:3080/dsh-ningning/balance.json  # 200 JSON（真实余额）
curl http://127.0.0.1:3080/dsh-ningning/size.json     # GET/PUT 读写回路
curl http://127.0.0.1:3080/dsh-ningning/widget.js     # 200 JS
curl http://127.0.0.1:3080/dsh-ningning/sound/press.mp3?set=duck  # 200 audio/mpeg
curl http://127.0.0.1:3080/   # index.html 含 widget.js 脚本标签
```

## 替换素材

同名覆盖 `assets/` 下的文件即可。注意区分两类加载方式：
- **音效（mp3）**：每次请求实时读盘，替换后**无需重启**，刷新页面即生效
- **形象图 / rua.gif**：由宿主进程内存缓存，替换后需**重启 DSH** 才会读到新字节

素材清单：
- `ningning1.png`：宠物主形象（透明底，已补边为方形画布，代码按 610×610 命中映射）
- `ningning2.png`：主图缺失时的兜底形象
- `Ya1.mp3` / `Ya2.mp3`：小黄鸭套按下/松手音效
- `D1.mp3` / `D2.mp3`：音效1套按下/松手音效
- `0721.mp3`：任务完成提示音（每轮对话结束后播放，路由 `/dsh-ningning/sound/task.mp3`）
- `rua.gif`：气泡随机动图（当前内容来自源素材 `rua ningning.gif`，112×112 五帧动画，同名覆盖为 `rua.gif`）

## 更新记录

### v0.1.1 — 2026-09-03

- 新增**任务完成提示音**：宿主监听会话事件，每轮对话结束（`turn/end`）后页面自动播放 `0721.mp3`（「请看我~」）
  - 新路由 `/dsh-ningning/sound/task.mp3`（实时读盘、no-store）
  - 1.5 秒防连发保护（子代理并行结束不爆音）；音量跟随挂件音量设置


## 致谢与许可

- 派生自 [DeepSeek-Balance-Whale-Widget](https://github.com/MeteorNOX/DeepSeek-Balance-Whale-Widget)（MIT, © 2026 MeteorNOX）
- 本包基于 MIT License 发布，详见 `LICENSE`
