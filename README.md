<p align="center">
  <a href="https://lightpanda.io"><img src="https://cdn.lightpanda.io/assets/images/logo/lpd-logo.png" alt="Logo" height=170></a>
</p>
<h1 align="center">Lightpanda Browser（中文汉化版）</h1>
<p align="center">
<strong>从零打造、为 AI 智能体与自动化而生的无头浏览器。</strong><br>
不是 Chromium 的分支，也不是 WebKit 的补丁版——一款用 Zig 全新编写的浏览器。<br>
比 Chromium 轻 16 倍，快 9 倍。
</p>

</div>

> [!NOTE]
> 本仓库是 [lightpanda-io/browser](https://github.com/lightpanda-io/browser) 的中文汉化版：README 与顶层说明文档翻译为中文，代码与上游保持同步。安装、构建等操作请以本说明与官方文档为准；上游最新进展见官方仓库。

<div align="center">

[![License](https://img.shields.io/github/license/lightpanda-io/browser)](https://github.com/lightpanda-io/browser/blob/main/LICENSE)
[![Twitter Follow](https://img.shields.io/twitter/follow/lightpanda_io)](https://twitter.com/lightpanda_io)
[![GitHub stars](https://img.shields.io/github/stars/lightpanda-io/browser)](https://github.com/lightpanda-io/browser)
[![Discord](https://img.shields.io/discord/1391984864894521354?style=flat-square&label=discord)](https://discord.gg/K63XeymfB5)

</div>
<div align="center">

[<img width="350px" src="https://cdn.lightpanda.io/assets/images/github/execution-time-v2.svg">
](https://github.com/lightpanda-io/demo)
&emsp;
[<img width="350px" src="https://cdn.lightpanda.io/assets/images/github/memory-frame-v2.svg">
](https://github.com/lightpanda-io/demo)
</div>

## 性能基准

在 AWS EC2 m5.large 实例上通过网络请求 933 个真实网页。
详见[基准测试详情](https://github.com/lightpanda-io/demo/blob/main/BENCHMARKS.md#crawler-benchmark)。

| 指标 | Lightpanda | 无头 Chrome | 差距 |
| :---- | :---- | :---- | :---- |
| 内存（峰值，100 页） | 123MB | 2GB | 约 1/16 |
| 执行耗时（100 页） | 5 秒 | 46 秒 | 快约 9 倍 |

## 快速开始

### 安装

**包管理器**

Homebrew 安装最新 nightly 版：
```console
brew install lightpanda-io/browser/lightpanda
```

Arch Linux 用户仓库（AUR）安装最新 nightly 版：
```console
yay -S lightpanda-nightly-bin
```

**从 nightly 构建下载**

可以从 [nightly 构建页](https://github.com/lightpanda-io/browser/releases/tag/nightly)下载 Linux 与 macOS、x86_64 与 aarch64 的最新二进制。

*Linux*
```console
curl -L -o lightpanda https://github.com/lightpanda-io/browser/releases/download/nightly/lightpanda-x86_64-linux && \
chmod a+x ./lightpanda
```

运行前先校验二进制：
```console
./lightpanda version
```

[Linux aarch64 版本同样可用](https://github.com/lightpanda-io/browser/releases/tag/nightly)

如果你使用 [**Nix**](https://nixos.org/download/)，可以直接通过 flake 运行 Lightpanda，无需下载二进制：
```console
nix run github:lightpanda-io/browser -- --help
```

也可以在自己的 flake 中将其固定为输入：
```nix
{
  inputs.lightpanda.url = "github:lightpanda-io/browser";
}
```

> **注意：** Linux 发行版二进制链接的是 glibc。在基于 musl 的发行版（Alpine 等）上会因缺少 glibc 动态加载器而报 `cannot execute: required file not found`。请改用基于 glibc 的基础镜像（如 `FROM debian:bookworm-slim` 或 `FROM ubuntu:24.04`），或[从源码构建](#从源码构建)。
>
> **Android / Termux：** 没有原生 Android 构建。Linux aarch64 二进制需要 glibc 加载器（`/lib/ld-linux-aarch64.so.1`），Android 的 Bionic libc 不提供，会报同样的 `cannot execute: required file not found` 错误。

*macOS*
```console
curl -L -o lightpanda https://github.com/lightpanda-io/browser/releases/download/nightly/lightpanda-aarch64-macos && \
chmod a+x ./lightpanda
```

[macOS x86_64 版本同样可用](https://github.com/lightpanda-io/browser/releases/tag/nightly)

*Windows + WSL2*

Lightpanda 没有原生 Windows 二进制。请按上述 Linux 步骤在 WSL 内安装。

没装 WSL？在管理员终端运行 `wsl --install`，重启后打开 `wsl`。
详见 [Microsoft 的 WSL 安装指南](https://learn.microsoft.com/en-us/windows/wsl/install)。

你的自动化客户端（Puppeteer、Playwright 等）既可以跑在 WSL 里，也可以跑在 Windows 宿主机上。WSL 会自动转发 `localhost:9222`。

**从 Docker 安装**

Lightpanda 为 Linux amd64 和 arm64 提供官方 [Docker 镜像](https://hub.docker.com/r/lightpanda/browser)。
以下命令拉取镜像并启动新容器，将 Lightpanda 的 CDP 服务暴露在 `9222` 端口：
```console
docker run -d --name lightpanda -p 127.0.0.1:9222:9222 lightpanda/browser:latest
```

### 抓取一个 URL

```console
./lightpanda fetch --obey-robots --dump html --log-format pretty  --log-level info https://demo-browser.lightpanda.io/campfire-commerce/
```

`--dump markdown` 可直接转换为 markdown；`--dump png > page.png` 或 `--dump pdf > page.pdf` 输出页面的纯文本渲染；`--dump structured_data` 把页面的 JSON-LD、OpenGraph、Twitter card、meta 和 link 标签按 JSON 打印，与 MCP 的 `structuredData` 工具一致。`--wait-until`、`--wait-ms`、`--wait-selector` 和 `--wait-script` 可调整抓取前的等待时间。

### 启动 CDP 服务

```console
./lightpanda serve --obey-robots --log-format pretty  --log-level info --host 127.0.0.1 --port 9222
```

改为监听 Unix 域套接字（仅属主可访问，权限 0600）而非 TCP：

```console
./lightpanda serve --unix-socket /run/user/1000/lightpanda.sock
```

CDP 服务启动后，配置 `browserWSEndpoint` 即可运行 Puppeteer 脚本。

<details>
<summary>Puppeteer 脚本示例</summary>

```js
import puppeteer from 'puppeteer-core';

// 用 browserWSEndpoint 传入 Lightpanda CDP 服务的地址。
const browser = await puppeteer.connect({
  browserWSEndpoint: "ws://127.0.0.1:9222",
});

// 脚本其余部分保持不变。
const context = await browser.createBrowserContext();
const frame = await context.newPage();

// 输出 frame 中的所有链接。
await frame.goto('https://demo-browser.lightpanda.io/amiibo/', {waitUntil: "networkidle0"});

const links = await frame.evaluate(() => {
  return Array.from(document.querySelectorAll('a')).map(row => {
    return row.getAttribute('href');
  });
});

console.log(links);

await frame.close();
await context.close();
await browser.disconnect();
```
</details>

#### 启动 Webdriver Bidi 服务

加 `--protocol webdriver` 启用 Bidi 支持。
可用 `--protocol webdriver --protocol cdp` 同时启动 CDP 与 Bidi。

```console
./lightpanda serve --obey-robots --log-format pretty  --log-level info --host 127.0.0.1 --port 9222 --protocol webdriver
```

### Agent 模式

`lightpanda agent` 让你用内置的智能体驱动浏览器。用自然语言或斜杠命令描述你想做什么，它就会操控浏览器：导航页面、点击流程、填写表单、提取结构化数据。与其说它是陪你聊天的 chatbot，不如说它是听你指挥上网干活的机器人。

由于智能体与浏览器运行在同一进程内，每次工具调用都是直接操作，Lightpanda 的速度和内存优势得以完整保留。

Agent 会话的产出是一份 [PandaScript](https://lightpanda.io/docs/usage/pandascript)：原汁原味的 JavaScript，内建了一小组 Lightpanda 原生浏览器原语。在会话中执行 `/save` 即可导出，再用 `lightpanda run <script>.js` 回放。脚本是确定性的、不消耗 token——你可以先用 LLM 做原型，再把产出的脚本部署到生产环境，运行时完全不依赖模型。

它支持 Anthropic、OpenAI、Gemini、Google Vertex AI、Mistral、Hugging Face，多厂商网关 [Vercel AI Gateway](https://vercel.com/ai-gateway) 与 [OpenRouter](https://openrouter.ai)（一个 key 调用数百个模型），通过 `OPENAI_BASE_URL` 接入任何 OpenAI 兼容端点，以及经 Ollama 或 llama.cpp 使用本地模型。还可以用 `--no-llm` 在不接 LLM 的情况下进入 REPL。完整参考见 [agent 文档](https://lightpanda.io/docs/usage/agent)。

```console
./lightpanda agent                                    # 自动从环境变量检测 API key
./lightpanda agent --task "top story on news.ycombinator.com?"
./lightpanda agent --no-llm                           # 基础 REPL，不用 LLM
./lightpanda run session.js                           # 运行已保存的脚本
cat session.js | ./lightpanda run -                   # …或经 stdin 管道传入
./lightpanda agent --provider gemini --task "..."     # 强制指定服务商
./lightpanda agent --list-models                      # 列出当前服务商可用模型
VERTEX_API_KEY=... ./lightpanda agent --provider vertex             # Vertex AI，express 模式
GOOGLE_CLOUD_PROJECT=my-proj ./lightpanda agent --provider vertex   # Vertex AI，经 gcloud 认证获取 token
AI_GATEWAY_API_KEY=... ./lightpanda agent --provider vercel --model moonshotai/kimi-k2   # Vercel AI Gateway 背后的任意模型
OPENROUTER_API_KEY=... ./lightpanda agent --provider openrouter --model anthropic/claude-sonnet-5   # OpenRouter 背后的任意模型
OPENAI_BASE_URL=https://my-gateway/v1 OPENAI_API_KEY=... ./lightpanda agent            # 任意 OpenAI 兼容服务
```

### 原生 MCP 与 skill

MCP 服务通过 stdio 上的 MCP JSON-RPC 2.0 通信。

加入你的 MCP 配置：
```json
{
  "mcpServers": {
    "lightpanda": {
      "command": "/path/to/lightpanda",
      "args": ["mcp"]
    }
  }
}
```

#### HTTP 传输与独立会话

若要用一个进程服务多个智能体，可给 MCP 服务指定端口，改用 HTTP 而非 stdio 启动（加 `--host x.x.x.x` 指定监听接口）：

```bash
lightpanda mcp --port 9223
```

客户端向 `http://host:9223/mcp` POST JSON-RPC。每个连接会被路由到各自的**浏览会话**——各自的页面、cookie 与内存，多个智能体不会再互相覆盖页面：

- `initialize` 时未带 `Mcp-Session-Id` 头的客户端会分配到全新会话；会话 id 在响应的 `Mcp-Session-Id` 头中返回。后续请求带上它即可保持在该会话中（**隔离**）。
- 两个智能体发送**相同**的 `Mcp-Session-Id` 则共享同一个浏览上下文（**共享**——例如多个智能体协作处理同一页面的工作流）。
- `session_new`、`session_list` 和 `session_close` 工具可显式管理会话。携带 `Mcp-Session-Id` 发送 `DELETE /mcp` 即关闭该会话。

[阅读完整文档](https://lightpanda.io/docs/open-source/guides/mcp-server)

另有配套 skill：[lightpanda-io/agent-skill](https://github.com/lightpanda-io/agent-skill)。

### Shell 补全

`lightpanda completion <bash | fish | zsh>` 输出针对命令、flag 及其取值的补全脚本：

```console
lightpanda completion fish > ~/.config/fish/completions/lightpanda.fish
lightpanda completion bash > ~/.local/share/bash-completion/completions/lightpanda
lightpanda completion zsh > ~/.zfunc/_lightpanda  # 需在 compinit 前设置 fpath=(~/.zfunc $fpath)
```

### 遥测

默认情况下，Lightpanda 会收集并发送使用遥测数据。设置环境变量 `LIGHTPANDA_DISABLE_TELEMETRY=true` 即可关闭。隐私政策见：[https://lightpanda.io/privacy-policy](https://lightpanda.io/privacy-policy)。

### Core dump

设置 `LIGHTPANDA_DISABLE_CORE_DUMP`（任意值）可在启动时将软性 `RLIMIT_CORE` 置零，从而禁止崩溃时生成 core dump。

## 项目状态

以下是我们已实现的关键特性。
完整细节见我们的 [Web Platform Tests 结果](https://perf.lightpanda.io/wpt)。

- [x] CORS
- [x] HTTP 加载器（[Libcurl](https://curl.se/libcurl/)）
- [x] HTML 解析器（[html5ever](https://github.com/servo/html5ever)）
- [x] DOM 树
- [x] JavaScript 支持（[v8](https://v8.dev/)）
- [x] DOM API
- [x] Ajax
  - [x] XHR API
  - [x] Fetch API
- [x] DOM 与 Markdown 输出
- [x] CDP/Websockets 服务
- [x] 点击
- [x] 表单输入
- [x] Cookies
- [x] 自定义 HTTP 头
- [x] 代理支持
- [x] 网络请求拦截
- [x] 以 `--obey-robots` 选项遵守 `robots.txt`
- [x] CDP 与 Webdriver Bidi
- [x] 广告拦截

## 从源码构建

### 前置要求

Lightpanda 使用 [Zig](https://ziglang.org/) `0.15.2` 编写。构建本项目必须安装对应版本。

Lightpanda 还依赖
[v8](https://chromium.googlesource.com/v8/v8.git)、
[Libcurl](https://curl.se/libcurl/) 与 [html5ever](https://github.com/servo/html5ever)。

为编译 v8 引擎，需要先安装一些依赖库：

**Debian/Ubuntu 系 Linux**：

```
sudo apt install xz-utils ca-certificates \
    pkg-config libglib2.0-dev \
    clang make curl git
```
还需要[安装 Rust](https://rust-lang.org/tools/install/)。

装有 [**Nix**](https://nixos.org/download/) 的系统可使用 devShell。
```
nix develop
```

**macOS** 需要 cmake 和 [Rust](https://rust-lang.org/tools/install/)。
```
brew install cmake
```

### 构建与运行

用 `make build` 构建完整浏览器，调试环境用 `make build-dev`。

也可以直接用 zig 命令：`zig build run`。

#### 内嵌 v8 snapshot

Lightpanda 使用 v8 snapshot。默认在启动时创建，你也可以用以下命令将其内嵌：

生成 snapshot。
```
zig build snapshot_creator -- src/snapshot.bin
```

使用 snapshot 二进制构建。
```
zig build -Dsnapshot_path=../../snapshot.bin
```

更多细节见 [#1279](https://github.com/lightpanda-io/browser/pull/1279)。

## 测试

### 单元测试

运行 `make test` 即可测试 Lightpanda。

```bash
make test                                       # 运行全部测试
make test F="server"                            # 按子串过滤
TEST_FILTER="WebApi: #selector_all" make test   # 过滤主测试 + 子测试（分隔符：#）
TEST_VERBOSE=true make test
TEST_FAIL_FIRST=true make test
METRICS=true make test                          # 以 JSON 采集分配/耗时指标
TEST_JOBS=1 make test                           # 单进程运行（默认最多 4 个进程，过滤时为 1）
```

### 端到端测试

运行端到端测试前，需要把 [demo 仓库](https://github.com/lightpanda-io/demo)克隆到 `../demo` 目录。

你需要安装 [demo 的 node 依赖](https://github.com/lightpanda-io/demo?tab=readme-ov-file#dependencies-1)。

还需要安装 [Go](https://go.dev) > v1.24。

```
make end2end
```

### Web Platform Tests

Lightpanda 针对标准化的 [Web Platform Tests](https://web-platform-tests.org/) 做测试。

我们使用[一个 fork](https://github.com/lightpanda-io/wpt/tree/fork)，内含自定义的
[`testharnessreport.js`](https://github.com/lightpanda-io/wpt/blob/fork/resources/testharnessreport.js)。结果[每天发布](https://perf.lightpanda.io/wpt)。

作为参考，你可以通过 [wpt.live](https://wpt.live) 在自己的浏览器里执行 WPT 测试用例。

#### 配置 WPT HTTP 服务

运行测试前，必须克隆仓库、配置自定义 hosts 并生成
`MANIFEST.json` 文件。

以 `fork` 分支克隆仓库。
```
git clone -b fork --depth=1 git@github.com:lightpanda-io/wpt.git
```

进入 `wpt/` 目录。

把自定义域名安装到 `/etc/hosts`
```
./wpt make-hosts-file | sudo tee -a /etc/hosts
```

生成 `MANIFEST.json`
```
./wpt manifest
```
细节见 [WPT 的环境搭建指南](https://web-platform-tests.org/running-tests/from-local-system.html)。

#### 运行 WPT 测试套件

[github.com/lightpanda-io/demo/](https://github.com/lightpanda-io/demo/)
仓库提供了一个外部 [Go](https://go.dev) 运行器，位于 `wptrunner/` 目录，需要先克隆该项目。

先在 `wpt/` 克隆目录里启动 WPT 的 HTTP 服务。
```
./wpt serve
```

启动一个 Lightpanda 浏览器

```
zig build -Dwpt_extensions run -- serve \
    --ws-max-concurrent 64 \
    --insecure-disable-tls-host-verification \
    --load-resources iframe \
    --load-resources image \
    --load-resources worker \
    --load-resources stylesheet
```

然后从 demo 克隆目录启动 wptrunner：
```
cd wptrunner && go run .
```

或只跑某个测试：

```
cd wptrunner && go run . Node-childNodes.html
```

`wptrunner` 命令接受 `--summary` 与 `--json` 选项改变输出形式，`--concurrency` 定义并发上限。

不用运行器、只想检查单个测试用例时，`fetch --dump wpt` 会以 JSON 打印结果：harness 状态、通过/失败汇总以及未通过的用例。它依赖 fork 的 `testharnessreport.js`。

```
zig build -Dwpt_extensions run -- fetch \
    --insecure-disable-tls-host-verification \
    --load-resources worker \
    --load-resources iframe \
    --dump wpt \
    --wait-script "window.report && report.complete" \
    "http://web-platform.test:8000/dom/nodes/Node-childNodes.html"
```

:warning: 跑完整测试套件耗时很长。这种情况下，用 `releaseFast` 模式构建可以显著加速。

```
zig build -Dwpt_extensions -Doptimize=fast run -- serve ...
```

## 参与贡献

贡献指引见 [CONTRIBUTING.md](CONTRIBUTING.md)。
在 pull request 流程中你必须签署我们的 [CLA](CLA.md)。
- [Discord](https://discord.gg/K63XeymfB5)

## 为什么选择 Lightpanda？

### 现代网页离不开 JavaScript 执行

过去，简单的 HTTP 请求就够 Web 自动化用了。现在不行了——如今驱动着大半个网页的是 JavaScript：

- Ajax、单页应用（SPA）、无限加载、即时搜索
- JS 框架：React、Vue、Angular 等等

### Chrome 不是合适的工具

在服务器上跑完整的桌面浏览器能行，但扩展性很差。成百上千个 Chrome 实例代价高昂：

- 吃内存、吃 CPU
- 难以大规模打包、部署和维护
- 大量特性在无头模式下根本用不上

### Lightpanda 为性能而生

要有真性能地支持 JavaScript，就意味着从零构建，而不是 fork Chromium：

- 不基于 Chromium、Blink 或 WebKit
- 用 Zig 编写——一门对内存有显式控制的底层语言
- 没有图形渲染引擎
