# 简介

基于 [v2fly/domain-list-community#256](https://github.com/v2fly/domain-list-community/issues/256) 的提议，重构 [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community) 的构建流程，并添加新功能。

本仓库同时作为**自定义域名列表的宿主**：`custom-data/` 目录下的文件会被并入构建，生成客户端可用的 `geosite:` 类别。

## 与官方版 `dlc.dat` 不同之处

- 将 `dlc.dat` 重命名为 `geosite.dat`
- 去除 `cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-!cn` 列表里带有 `@ads`、`@cn` 属性的规则，尽量避免在中国大陆有接入点的海外公司的域名走代理。例如，避免国区 Steam 游戏下载服务走代理。

## 自定义列表（`custom-data/`）

一个文件即一个类别，**文件名即类别名**。例如 `custom-data/mygames`：

```
calamity-overhaul.cc          默认：域名后缀匹配（含所有子域）
full:download.epicgames.com   完整匹配
keyword:example               子串匹配
regexp:^ad\.                  正则匹配
include:google @cn            引用其他列表（可带属性）
行尾也可直接加属性，如 example.com @cn
```

构建后会生成对应的 `geosite:mygames` 类别。

> 注意：这类列表**需要在客户端路由规则里显式引用才会生效**。如果目的只是让某些域名直连，写进 [@imacte/v2ray-rules-dat](https://github.com/imacte/v2ray-rules-dat) 的 `hidden` 分支 `direct.txt` 更省事——它会并入 `geosite:cn`，被现有规则自动覆盖。

## 构建流程

- 本仓库**自身的构建 workflow 已停用**（`.github/workflows/build.yml` 已改名为 `build.yml.disabled`）。GitHub 只读取 `.github/workflows/` 下的 `.yml` / `.yaml`，改名即停用；想恢复把名字改回来即可。
- `geosite.dat` 由 [@imacte/v2ray-rules-dat](https://github.com/imacte/v2ray-rules-dat) 统一构建：其 `run.yml` 会 checkout 本仓库源码，执行 `go run ./ --datapath=../community/data`，并把 `custom-data/*` 拷贝进构建数据目录。

## 下载地址

构建产物统一发布在 [@imacte/v2ray-rules-dat](https://github.com/imacte/v2ray-rules-dat) 的 Release 与 `release` 分支：

- <https://raw.githubusercontent.com/imacte/v2ray-rules-dat/release/geosite.dat>
- <https://github.com/imacte/v2ray-rules-dat/releases/latest/download/geosite.dat>

## 维护

上游 [@Loyalsoldier/domain-list-custom](https://github.com/Loyalsoldier/domain-list-custom) 更新频率很低（一年数次）。若希望本仓库的编译器代码跟上上游改动，在本仓库点击 **Sync fork** 即可。