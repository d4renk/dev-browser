# Dev Browser 中文使用教程

Dev Browser 是 Claude Code 的一个“眼睛”和“手”，让 AI 能够像开发者一样控制浏览器：查看页面、点击按钮、测试流程等。

它有两种工作模式，根据你的需求选择：

1.  **扩展模式 (Extension Mode)**: 连接到你**当前打开**的 Chrome 浏览器。AI 可以看到你看到的页面，使用你的登录状态。
2.  **独立模式 (Headless/Standalone Mode)**: AI 自动下载并启动一个独立的 Chromium 浏览器。干净环境，适合自动化测试。

---

## 🚀 快速开始（我只想用扩展连接我的 Chrome）

这是最常用的模式。

### 第一步：准备服务端

服务端（Relay Server）是连接 Claude 和浏览器的桥梁。

1.  打开终端，进入服务端目录：
    ```bash
    cd skills/dev-browser
    ```
2.  安装依赖（如果提示下载 Chromium 且你不想下载，可忽略，只需确保依赖安装完成）：
    ```bash
    npm install
    ```
3.  **启动中继服务**（这一步至关重要，不要关闭终端）：
    ```bash
    npm run start-extension
    ```
    > **注意**: 这里使用 `start-extension` 而不是 `start-server`，因为它只启动连接中继，不需要检查或启动 Playwright 浏览器。

### 第二步：安装并配置 Chrome 插件

1.  **下载/构建插件**:
    如果你下载的是源码，需要构建插件：

    ```bash
    cd extension
    npm install
    npm run build
    ```

    （构建完成后，生成的文件在 `extension/.output/chrome-mv3`）

2.  **加载到 Chrome**:
    - 打开 Chrome 浏览器，输入 `chrome://extensions/`。
    - 打开右上角的 **"开发者模式 (Developer mode)"**。
    - 点击 **"加载已解压的扩展程序 (Load unpacked)"**。
    - 选择 `extension/.output/chrome-mv3` 文件夹。

3.  **连接**:
    - 点击浏览器工具栏的 Dev Browser 图标。
    - 将开关切换到 **"Active"**。
    - 状态应显示为 **"Connected to relay"**。

### 第三步：连接 Claude

在 Claude Code 中运行：

```bash
/plugin marketplace add sawyerhood/dev-browser
/plugin install dev-browser@sawyerhood/dev-browser
```

然后你就可以说：

> "Connect to my browser and go to localhost:3000"
> (连接到我的浏览器并打开 localhost:3000)

---

## 🛠️ 独立模式（自动化测试用）

如果你希望 Claude 在后台自己跑测试，不干扰你的工作：

1.  进入目录：`cd skills/dev-browser`
2.  启动服务：`npm run start-server`
    - 这个命令会自动检查 Playwright 环境。如果没有，它会自动下载 Chromium。
    - 它会启动一个包含完整浏览器控制逻辑的服务器。

---

## ❓ 常见问题 FAQ

### Q1: 为什么 `npm install` 总是要下载 Chromium？

Dev Browser 默认依赖 Playwright 来实现浏览器控制。Playwright 为了保证稳定性，会下载一个特定版本的 Chromium。
如果你**只使用扩展模式**，可以通过以下方式跳过下载：

```bash
# 在 skills/dev-browser 目录下
PLAYWRIGHT_SKIP_BROWSER_DOWNLOAD=1 npm install
```

### Q2: 扩展一直显示 "Connecting..."

这说明扩展程序找不到本地的中继服务器。

1.  检查你是否运行了 `npm run start-extension` (或 `start-server`)。
2.  检查终端是否有报错。
3.  默认端口是 `9222`，确保没有被占用。

### Q3: `npm run start-server` 报错说 "Missing script: start-server"

你可能在项目的**根目录**下运行了命令。
请先进入子目录：

```bash
cd skills/dev-browser
```

然后再运行命令。

### Q4: Claude 总是尝试启动新浏览器，而不是用我的？

确保你在 Prompt 中明确指示，或者确保扩展已连接。通常 Claude 会优先检查是否有活跃的连接。你可以明确说：

> "Use the connected extension to..." (使用已连接的扩展来...)
