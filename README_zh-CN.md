<p align="center">
  <img src="assets/header.png" alt="Dev Browser - Claude Code 的浏览器自动化" width="100%">
</p>

一个用于 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 的浏览器自动化插件，允许 Claude 控制您的浏览器，以便在开发过程中测试和验证您的工作。

**主要特性：**

- **持久化页面** - 一次导航，跨多个脚本交互
- **灵活执行** - 尽可能运行完整脚本，探索时逐步执行
- **LLM 友好的 DOM 快照** - 为 AI 优化的结构化页面检查

## 先决条件

- 已安装 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI
- [Node.js](https://nodejs.org) (v18 或更高版本) 及 npm

## 安装

### Claude Code

```
/plugin marketplace add sawyerhood/dev-browser
/plugin install dev-browser@sawyerhood/dev-browser
```

安装后重启 Claude Code。

### Amp / Codex

将技能复制到您的 skills 目录：

```bash
# 对于 Amp: ~/.claude/skills | 对于 Codex: ~/.codex/skills
SKILLS_DIR=~/.claude/skills  # 或 ~/.codex/skills

mkdir -p $SKILLS_DIR
git clone https://github.com/sawyerhood/dev-browser /tmp/dev-browser-skill
cp -r /tmp/dev-browser-skill/skills/dev-browser $SKILLS_DIR/dev-browser
rm -rf /tmp/dev-browser-skill
```

**仅限 Amp：** 使用前手动启动服务器：

```bash
cd ~/.claude/skills/dev-browser && npm install && npm run start-server
```

### Chrome 扩展（可选）

Chrome 扩展允许 Dev Browser 控制您现有的 Chrome 浏览器，而不是启动单独的 Chromium 实例。这使您可以访问已登录的会话、书签和扩展程序。

**安装：**

1. 从 [最新版本](https://github.com/sawyerhood/dev-browser/releases/latest) 下载 `extension.zip`
2. 将文件解压到一个永久位置（例如 `~/.dev-browser-extension`）
3. 打开 Chrome 并转到 `chrome://extensions`
4. 启用“开发者模式”（右上角开关）
5. 点击“加载已解压的扩展程序”并选择解压后的扩展文件夹

**使用扩展：**

1. 点击 Chrome 工具栏中的 Dev Browser 扩展图标
2. 将其切换为“Active” - 这将启用浏览器控制
3. 让 Claude 连接到您的浏览器（例如，“connect to my Chrome” 或 “use the extension”）

激活时，Claude 可以控制您现有的 Chrome 标签页，并保留所有已登录的会话、Cookie 和扩展程序。

## 权限

要跳过权限提示，请添加以下内容到 `~/.claude/settings.json`：

```json
{
  "permissions": {
    "allow": ["Skill(dev-browser:dev-browser)", "Bash(npx tsx:*)"]
  }
}
```

或者运行 `claude --dangerously-skip-permissions` (跳过所有提示)。

## 用法

只需让 Claude 与您的浏览器交互：

> "Open localhost:3000 and verify the signup flow works"
> (打开 localhost:3000 并验证注册流程是否正常工作)

> "Go to the settings page and figure out why the save button isn't working"
> (转到设置页面，找出保存按钮不起作用的原因)

## 基准测试

| 方法                    | 时间    | 成本  | 轮数 | 成功率 |
| ----------------------- | ------- | ----- | ---- | ------ |
| **Dev Browser**         | 3m 53s  | $0.88 | 29   | 100%   |
| Playwright MCP          | 4m 31s  | $1.45 | 51   | 100%   |
| Playwright Skill        | 8m 07s  | $1.45 | 38   | 67%    |
| Claude Chrome Extension | 12m 54s | $2.81 | 80   | 100%   |

_查看 [dev-browser-eval](https://github.com/SawyerHood/dev-browser-eval) 了解方法论。_

### 有何不同

| 方法                                                             | 工作原理                                          | 权衡                                                   |
| ---------------------------------------------------------------- | ------------------------------------------------- | ------------------------------------------------------ |
| [Playwright MCP](https://github.com/microsoft/playwright-mcp)    | 使用单独工具调用的 观察-思考-行动 循环            | 简单但缓慢；每个动作都是单独的往返                     |
| [Playwright Skill](https://github.com/lackeyjb/playwright-skill) | 端到端运行的完整脚本                              | 快速但脆弱；脚本每次都重新开始                         |
| **Dev Browser**                                                  | 有状态服务器 + 代理脚本执行                       | 两者兼得：持久化状态与灵活执行                         |

## 许可证

MIT

## 作者

[Sawyer Hood](https://github.com/sawyerhood)
