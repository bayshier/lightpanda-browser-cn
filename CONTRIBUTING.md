# 参与贡献

Lightpanda 通过 GitHub 接收 pull request。

## 开发

- 运行测试：`make test`
- 检查格式：`zig fmt --check ./*.zig ./**/*.zig`

测试、格式化与代码约定的完整集合（测试过滤器、泄漏检测不变式、`@import` 别名大小写、struct-init 推断）见 [AGENTS.md](AGENTS.md)。

### 跳过 V8 源码编译

默认情况下，构建会从源码编译 V8，需要数分钟。执行一次
`make download-v8`，改为从
[`zig-v8-fork`](https://github.com/lightpanda-io/zig-v8-fork/releases)
的 release 拉取匹配的预编译库；之后 `make build` / `make test` 会自动使用它们。在 Linux
x86_64 上还包括 `-Ddev_fast` 构建（默认开启）所用的共享版本
（`libc_v8.so`）。传 `-Ddev_fast=false` 则改为静态链接 V8 的调试构建。

## 提 PR 之前

- [ ] 测试通过（`make test`）。
- [ ] 格式检查干净（`zig fmt --check ./*.zig ./**/*.zig`）。
- [ ] 已签署 CLA（见下文）。

## CLA

首次提交 pull request 时必须签署我们的 [CLA](CLA.md)，
否则我们无法接收你的贡献。

签名流程使用 [CLA assistant
lite](https://github.com/marketplace/actions/cla-assistant-lite)。流程示例见 [#303](https://github.com/lightpanda-io/browser/pull/303)。
