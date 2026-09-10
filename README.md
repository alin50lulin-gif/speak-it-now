# Speak It Now

一款用于网页英语学习和听力训练的 Chrome 扩展。它可以朗读整个网页、选中的文本或鼠标悬停的段落，并在朗读过程中同步高亮当前句子和单词。

本项目基于开源项目 [Spartan-71/Read-It-Out](https://github.com/Spartan-71/Read-It-Out) 修改和扩展。感谢原作者提供浏览器朗读、浮动控制器、多语音引擎和 Kokoro 本地语音等基础能力。

## 本版本新增功能

在 Read It Out 原有功能基础上，本项目主要增加了以下学习辅助功能：

- 段落悬停朗读：鼠标移到正文段落后自动浅色标记，并在段落附近显示迷你播放控制器。
- 选区优先：选中一个或多个段落后播放时，只朗读选中内容；没有选区时才朗读整个网页。
- 逐句和逐词高亮：当前句子使用浅色背景，正在朗读的单词使用深色背景。
- 高亮开关：初学者可以开启跟读高亮，进阶用户可以关闭高亮进行盲听训练。
- 多段落高亮跟随：跨段落朗读时，每个段落使用独立的 DOM 范围，降低第二段开始后的字符偏移。
- 生词本：选中单词或短语后点击 `＋`，保存到浏览器本地生词本。
- AI 释义：自动生成简体中文释义、英文例句和例句中文翻译。
- 生词发音：在 Vocabulary 页面点击 `🔊` 播放单词发音。
- OpenAI 兼容接口：生词释义支持自定义 API Key、Base URL 和模型名称，可接入兼容 OpenAI Chat Completions API 的第三方模型。
- Kokoro 降级播放：所选文本无法由 Kokoro 生成时，可自动回退到浏览器语音。

## 原有核心功能

- 浮动网页播放器：播放、暂停、速度调节和阅读进度。
- 选中文本迷你播放器。
- Browser Speech、Kokoro Local、ElevenLabs、OpenAI、Sarvam AI 和 Smallest AI 等语音引擎。
- `0.75x`、`1x`、`1.25x`、`1.5x` 和 `2x` 播放速度。
- 可独立开关选区弹窗、浮动图标及跟读高亮。
- API Key 保存在 Chrome 扩展本地存储中。

## 安装

本项目不需要构建步骤。

1. 克隆仓库：

   ```bash
   git clone https://github.com/alin50lulin-gif/speak-it-now.git
   ```

2. 打开 Chrome，在地址栏输入 `chrome://extensions`。
3. 开启右上角的“开发者模式”。
4. 点击“加载已解压的扩展程序”。
5. 选择克隆后的 `speak-it-now` 文件夹。
6. 刷新已经打开的网页。

## Kokoro 本地模型说明

GitHub 仓库不包含体积较大的 ONNX 模型、WASM 运行时和语音 `.bin` 文件。它们在本地开发目录中保留，但已通过 `.gitignore` 排除。

如果只使用 Browser Speech 或云端语音服务，可以直接加载源码。若要使用 Kokoro Local，需要自行补齐以下资源：

```text
models/kokoro/onnx/model_quantized.onnx
vendor/onnxruntime-web/ort-wasm-simd-threaded.jsep.wasm
models/kokoro/voices/*.bin
```

这些资源可以从[上游 Read It Out 项目](https://github.com/Spartan-71/Read-It-Out)及其发布包中获取。大型模型更适合通过 Git LFS 或 GitHub Release 分发。

## 使用方法

### 朗读段落

1. 将鼠标移到正文段落。
2. 段落出现浅色预选效果和迷你播放器。
3. 点击播放按钮即可朗读该段落。

也可以手动选择一段或多段文本，再使用出现的迷你播放器。

### 跟读与盲听

打开扩展设置，在 `Page Controls` 中切换 `Follow spoken words`：

- 开启：显示当前句子和当前单词，适合跟读。
- 关闭：隐藏跟读高亮，适合盲听。

### 添加生词

1. 在网页中选择一个单词或短语。
2. 点击迷你播放器中的 `＋`。
3. 打开扩展设置中的 `Vocabulary` 页面查看生词。

生词会先保存到本地，然后再请求 AI 生成释义。因此即使模型请求失败，单词本身也不会丢失。

## 配置 AI 生词释义

进入 `Vocabulary` 页面，填写：

- `API Key`
- `OpenAI-compatible Base URL`
- `Model`

OpenAI 官方配置示例：

```text
Base URL: https://api.openai.com/v1
Model: gpt-4o-mini
```

第三方服务示例：

```text
Base URL: https://api.example.com/v1
Model: your-model-name
```

Base URL 也可以直接填写以 `/chat/completions` 结尾的完整地址。首次保存第三方域名时，Chrome 会请求相应的网络访问权限。

## 隐私说明

- 设置、生词及 API Key 保存在浏览器本地扩展存储中。
- 使用 Browser Speech 或本地 Kokoro 时，正文不需要发送给云端 TTS 服务。
- 使用云端语音或 AI 生词释义时，相应文本会发送至用户配置的服务地址。
- 请只使用你信任的第三方 API Base URL。

## 项目结构

```text
├── manifest.json             Chrome MV3 配置
├── background.js             后台服务、TTS 路由和生词释义请求
├── popup.html / popup.js     设置与生词本界面
├── panel.css                 弹窗及网页播放器样式
├── config.js                 语音、语言和设置定义
├── content/                  网页播放器、悬停选择与高亮逻辑
├── speech-engines/           云端语音引擎适配器
├── offscreen/                Kokoro 本地语音执行页面
├── models/                   Kokoro 配置及本地模型目录
└── vendor/                   Kokoro 与 ONNX Runtime 浏览器运行代码
```

## 致谢与上游

- 上游项目：[Spartan-71/Read-It-Out](https://github.com/Spartan-71/Read-It-Out)
- 当前仓库：[alin50lulin-gif/speak-it-now](https://github.com/alin50lulin-gif/speak-it-now)

本仓库是在 Read It Out 基础上进行的学习场景增强版本。上游项目的版权、声明及第三方依赖信息请参阅其原始仓库和本项目中的 `vendor/NOTICE.md`。
