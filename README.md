# 简介

基于 [v2fly/domain-list-community#256](https://github.com/v2fly/domain-list-community/issues/256) 的提议，重构 [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community) 的构建流程，并添加新功能。

## 与官方版 `dlc.dat` 不同之处

- 将 `dlc.dat` 重命名为 `geosite.dat`
- 去除 `cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-!cn` 列表里带有 `@ads`、`@cn` 属性的规则，尽量避免在中国大陆有接入点的海外公司的域名走代理。例如，避免国区 Steam 游戏下载服务走代理。

## 构建流程

- 本仓库自行构建并发布 `geosite.dat`：数据来自 [@v2fly/domain-list-community](https://github.com/v2fly/domain-list-community) 的 `data` 目录，构建方式与上游一致
- 触发：push 到 `master`、手动 Run workflow、以及**每天 21:30 UTC** 的定时任务；当上游 `domain-list-community` 的最新 tag 与本仓库最新 Release 的 tag 相同时会跳过构建（数据没变就不重复构建）
- [@imacte/v2ray-rules-dat](https://github.com/imacte/v2ray-rules-dat) 也会 checkout 本仓库的源码来构建它自己的 `geosite.dat`——两者的数据源与产物不同，互不影响

## 下载地址

- 本仓库自建（纯 `domain-list-community` 数据，`geosite:cn` 约 5 千条）：<https://github.com/imacte/domain-list-custom/releases/latest/download/geosite.dat>
- 与上游扩展列表合并后的完整版（`geosite:cn` 约 11 万条）：<https://github.com/imacte/v2ray-rules-dat/releases/latest/download/geosite.dat>

## 使用本项目的项目

[@imacte/v2ray-rules-dat](https://github.com/imacte/v2ray-rules-dat)

## 维护

- 上游 [@Loyalsoldier/domain-list-custom](https://github.com/Loyalsoldier/domain-list-custom) 更新频率很低（一年数次）。若希望本仓库的编译器代码跟上上游改动，在本仓库点击 **Sync fork** 即可
- 本仓库的构建 workflow 与上游相比只多了一处容错：`Compare latest tags and set variables` 步骤里两处 `curl | grep | cut` 追加了 `|| true`。原因是 **fork 仓库在还没有任何 Release 时**，该命令取不到值，而 GitHub Actions 默认的 `-eo pipefail` 会让整步失败（上游自己有 Release，不会遇到）