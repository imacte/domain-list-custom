> **本仓库是上游的 fork，用于个人自建的 geo 规则流水线。**
>
> 三个仓的分工：
>
> | 仓库 | 角色 |
> |---|---|
> | [`imacte/domain-list-custom`](https://github.com/imacte/domain-list-custom) | `geosite.dat` 的**编译器** + 自定义域名列表（`custom-data/`） |
> | [`imacte/geoip`](https://github.com/imacte/geoip) | **IP 数据**（`geoip.dat`、`Country.mmdb`、`geoip-only-cn-private.dat`） |
> | [`imacte/v2ray-rules-dat`](https://github.com/imacte/v2ray-rules-dat) | **主力产线**：取上面两者的产物 + 外部列表，发布 `geoip.dat` + `geosite.dat` |
>
> 客户端（v2rayN 等）只需要填一个地址：
> `https://raw.githubusercontent.com/imacte/v2ray-rules-dat/release/{0}.dat`
>
> ---
## 本 fork 做了什么

1. 新增 **`custom-data/`** 目录，存放自己的域名列表（当前有 `mygames`，4 条）。
2. 在 `build.yml` 里加了一步：把 `custom-data/*` 拷进构建数据目录。
3. **停用了自身的构建 workflow**（`.github/workflows/build.yml` → `build.yml.disabled`）。本仓现在只作为**编译器 + 自定义列表宿主**，`geosite.dat` 由 [`imacte/v2ray-rules-dat`](https://github.com/imacte/v2ray-rules-dat) 统一构建。
   - GitHub 只读取 `.github/workflows/` 下的 `.yml` / `.yaml`，改名即停用；想恢复就把名字改回来。

## `custom-data/` 怎么用

**一个文件 = 一个列表，文件名就是列表名。**例如 `custom-data/mygames`：

```
calamity-overhaul.cc          默认=域名后缀匹配（含所有子域）
full:download.epicgames.com   精确匹配
keyword:example               子串匹配
regexp:^ad\.                  正则匹配
include:google @cn            引用别的列表（可带属性）
行尾也可以加属性，如 example.com @cn
```

生成后可在客户端写 `geosite:mygames` 引用。

⚠️ **它不会自动生效**——必须在路由规则里引用 `geosite:<列表名>`。如果目的只是“让某些域名直连”，更简单的是写进 [`imacte/v2ray-rules-dat`](https://github.com/imacte/v2ray-rules-dat) 的 `hidden` 分支 `direct.txt`（会并进 `cn` 列表，被现有规则直接覆盖）。

## 被谁消费

[`imacte/v2ray-rules-dat`](https://github.com/imacte/v2ray-rules-dat) 的 `run.yml` 会 checkout 本仓源码，执行 `go run ./ --datapath=../community/data` 编译 `geosite.dat`，并把 `custom-data/*` 拷进数据目录。

## 维护提醒

上游 [Loyalsoldier/domain-list-custom](https://github.com/Loyalsoldier/domain-list-custom) 一年只更新几次；想让编译器的改动跟上来，在本仓点 **Sync fork** 即可。

---
# 简介

基于 [v2fly/domain-list-community#256](https://github.com/v2fly/domain-list-community/issues/256) 的提议，重构 [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community) 的构建流程，并添加新功能。

## 与官方版 `dlc.dat` 不同之处

- 将 `dlc.dat` 重命名为 `geosite.dat`
- 去除 `cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-cn` 列表里带有 `@ads`、`@!cn` 属性的规则
- 去除 `geolocation-!cn` 列表里带有 `@ads`、`@cn` 属性的规则，尽量避免在中国大陆有接入点的海外公司的域名走代理。例如，避免国区 Steam 游戏下载服务走代理。

## 下载地址

[https://github.com/Loyalsoldier/domain-list-custom/releases/latest/download/geosite.dat](https://github.com/Loyalsoldier/domain-list-custom/releases/latest/download/geosite.dat)

## 使用本项目的项目

[@Loyalsoldier/v2ray-rules-dat](https://github.com/Loyalsoldier/v2ray-rules-dat)
